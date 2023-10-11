<template>
  <div id="app">
    <div id="map"></div>
    <control @update="update" @changeProjection="changeProjection"></control>
    <info :conInfo="conInfo" :orig-geo-data="origGeodata.features" @clicked="onClickChild"></info>
    <AbscondingHist :absconding-history="absconderHistory" @clicked="closeComponent" v-if="showHistory"></AbscondingHist>

  </div>
</template>

<script>
import * as d3 from 'd3';
import mapboxgl from 'mapbox-gl';
import control from './components/controlPanel.vue';
import info from './components/infoPanel.vue';
import AbscondingHist from "@/components/AbscondingHist.vue";





export default {
  name: 'App',
  components: {
    AbscondingHist,
    control,
    info
  },
  data() {
    return {
      map: null,
      accessToken: 'pk.eyJ1IjoibW9uYWxlbmEiLCJhIjoiY2l6dzFuenBzMDFvYjMyazdhcWMwd2dsMCJ9.xlKLbnrGSgAjxHLOM6JbVw',
      origGeodata:{},
      filterYear: 1835,
      filterGender: 0,
      conInfo: [{}],
      absconderHistory: [{}],
      showHistory: false
    }
  },
  methods: {
    onClickChild (value) {
      this.absconderHistory = value;
      this.showHistory = true;
      /*console.log(value) // someValue*/
    },
    closeComponent () {
      this.showHistory = false;
    },

    addMapLayers: function() {
        this.map.addSource('absconderData', {
          type: 'geojson',
          data: this.filterData(),
          cluster: true,
          clusterMaxZoom: 1, // Max zoom to cluster points on
          clusterRadius: 50
        });

        this.map.addLayer({
          id: 'absconders',
          type: 'circle',
          source: 'absconderData',
          paint: {
            //'circle-color': '#25707f',
            'circle-color': [
              'match',
              ['get', 'col'],
              1,
              '#fbb03b',
              0,
              '#223b53',
              /* other */ '#ccc'
            ],

            // 'circle-radius': ['*', 1, ['number', ['get', 'size'], 10]],
            // 'circle-radius': ['get', 'size'],
            'circle-radius':
                    [
                      'case',
                      ['>=', ['number', ['get', 'size']], 10],
                      ['get', 'size'],
                      ['<', ['number', ['get', 'size']], 10],
                      10,['get', 'size']
                    ],
            'circle-opacity': 0.8,
            'circle-stroke-width': 1,
            'circle-stroke-color': '#fff'
          },
        });

        this.map.addLayer({
          id:'absconder-counts',
          type: 'symbol',
          source: 'absconderData',
          layout: {
            'text-field': '{size}',
            'text-font': ['DIN Offc Pro Medium', 'Arial Unicode MS Bold'],
            'text-size': 12
          }
        });

        this.map.on('click', 'absconders', (e) => {
          this.conInfo = JSON.parse(e.features[0].properties.data);
        });

        this.map.on('mouseenter', 'absconders', () => {
          this.map.getCanvas().style.cursor = 'pointer';
        });

        this.map.on('mouseleave', 'absconders', () => {
          this.map.getCanvas().style.cursor = '';
        });

    },

    changeProjection: function(projection) {
      if (this.map.getLayer('absconders')) {
        this.map.removeLayer('absconders');
      }
      if (this.map.getLayer('absconder-counts')) {
        this.map.removeLayer('absconder-counts');
      }
      if (this.map.getSource('absconderData')) {
        this.map.removeSource('absconderData');
      }

      if (projection == 'frank') {
        this.map.setStyle('mapbox://styles/monalena/ck93lkfz50h1g1ipiaut42uhw');
      } else {
        this.map.setStyle('mapbox://styles/mapbox/satellite-v9');
      }

    },

    createFilter: function(year, gender) {
      if (gender > 0) {
        return function (f) {
          return "properties" in f &&
                  'YearAbsconded' in f['properties'] && f['properties']['YearAbsconded'] === year &&
                  'Gender' in f['properties'] && f['properties']['Gender'] == gender;
        }
      } else {
        return function (f) {
          return "properties" in f &&
                  'YearAbsconded' in f['properties'] && f['properties']['YearAbsconded'] === year;
        }
      }
    },

    filterData: function() {
      let f = this.createFilter(this.filterYear, this.filterGender);
      let filteredFeatures = this.origGeodata["features"].filter(f);
      let coordLookup = {};
      for (let i = 0; i < filteredFeatures.length; i++) {
        let coord = filteredFeatures[i].geometry.coordinates;
        if (coord in coordLookup) {
          coordLookup[coord]['properties'].push(filteredFeatures[i].properties);
        } else {
          coordLookup[coord] = {'coordinates' : coord, 'properties' : [filteredFeatures[i].properties],
            'col' : filteredFeatures[i].properties.government
          };
        }
      }
      let features = [];
      for (let coord in coordLookup) {
        features.push({'geometry': {'coordinates': coordLookup[coord]['coordinates']},
          'properties': {'size': coordLookup[coord]['properties'].length,
            'col': coordLookup[coord].col,
          'data': coordLookup[coord].properties}});
      }
      let filteredGeodata = {"type" : "FeatureCollection", "features" : features };
      // console.log("filteredGeodata",filteredGeodata);
      return filteredGeodata;
    },

    update: function(year, selected) {
      // update the map filter
      // Year
      this.filterYear = year;
      // Gender
      if (selected === 'all') {
        // `null` would not work for combining filters
        this.filterGender = 0;
      } else if (selected === 'female') {
        this.filterGender = 2;
      } else if (selected === 'male') {
        this.filterGender = 1;
      } else {
        console.log('error');
      }
      // Update map
      this.map.getSource('absconderData').setData(this.filterData());

    }
  },
  mounted() {
    // load data
    var dataLoaded = data => {

      for (var i = 0; i < data.features.length; i++) {
        data.features[i].properties.newDate = new Date(data.features[i].properties.Date);
        if (data.features[i].properties.ConvictPlaceCode) {
          // If true, add the 'government' property with value 1
          data.features[i].properties.government = 1;
        } else {
          data.features[i].properties.government = 0;
        }
      }

      // console.log(data.features[0].properties.newDate);
      // console.log(data.features[0].properties.Date);

      this.origGeodata = data;

      this.map.on('load', () => {
        this.addMapLayers();
      });
      this.map.on('style.load', () => {
        this.addMapLayers();
      });
    }

    // load map
    mapboxgl.accessToken = this.accessToken;

    this.map = new mapboxgl.Map({
      container: 'map', // container id
      style: 'mapbox://styles/monalena/ck93lkfz50h1g1ipiaut42uhw', // stylesheet location
      // style: 'mapbox://styles/mapbox/satellite-v9',  // satellite projection from mapbox
      center: [147.242,-42.604], // starting position [lng, lat]
      zoom: 8 // starting zoom
    });


    d3.json(process.env.BASE_URL+"data.geojson").then(dataLoaded).catch(console.log.bind(console));

  }
}
</script>

<style>
  body {
    margin: 0;
    padding: 0;
    font-family: 'Helvetica Neue', Helvetica, Arial, Sans-serif;
  }

  #map {
    position: absolute;
    top: 0;
    bottom: 0;
    width: 100%;
  }

  h1 {
    font-size: 20px;
    line-height: 30px;
  }

  h2 {
    font-size: 14px;
    line-height: 20px;
    margin-bottom: 10px;
  }

  a {
    text-decoration: none;
    color: #2dc4b2;
  }
</style>

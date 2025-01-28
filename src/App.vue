<template>
  <div id="app">
    <div id="map"></div>
    <control @update="update" ></control>
    <info :info="info" :orig-geo-data="origGeodata.features"></info>

  </div>
</template>

<script>
import * as d3 from 'd3';
import mapboxgl from 'mapbox-gl';
import control from './components/controlPanel.vue';
import info from './components/infoPanel.vue';





export default {
  name: 'App',
  components: {
    control,
    info
  },
  data() {
    return {
      map: null,
      accessToken: 'pk.eyJ1IjoibW9uYWxlbmEiLCJhIjoiY2l6dzFuenBzMDFvYjMyazdhcWMwd2dsMCJ9.xlKLbnrGSgAjxHLOM6JbVw',
      origGeodata:{},
      filterYear: 1829,
      info: [{}]
    }
  },
  methods: {

    addMapLayers: function() {
        this.map.addSource('gangsData', {
          type: 'geojson',
          data: this.filterData(),
          cluster: true,
          clusterMaxZoom: 1, // Max zoom to cluster points on
          clusterRadius: 50
        });

        this.map.addLayer({
          id: 'gangs',
          type: 'circle',
          source: 'gangsData',
          paint: {
            'circle-color': '#25707f',
            //'circle-color': [
            //  'match',
            //  ['get', 'col'],
            //  1,
            //  '#fbb03b',
            //  0,
            //  '#223b53',
            //  /* other */ '#ccc'
            //],

            //'circle-radius': ['*', 1, ['number', ['get', 'size'], 10]],
            //'circle-radius': ['get', 'size'],
            'circle-radius':
                    [
                      'case',
                      ['>=', ['number', ['get', 'size']], 10],
                      ['get', 'circlesize'],
                      ['<', ['number', ['get', 'size']], 10],
                      10,['get', 'circlesize']
                    ],
            'circle-opacity': 0.8,
            'circle-stroke-width': 1,
            'circle-stroke-color': '#fff'
          },
        });

        this.map.addLayer({
          id:'absconder-counts',
          type: 'symbol',
          source: 'gangsData',
          layout: {
            'text-field': '{size}',
            'text-font': ['DIN Offc Pro Medium', 'Arial Unicode MS Bold'],
            'text-size': 12
          }
        });

        this.map.on('click', 'gangs', (e) => {
          this.info = JSON.parse(e.features[0].properties.data);
        });

        this.map.on('mouseenter', 'gangs', () => {
          this.map.getCanvas().style.cursor = 'pointer';
        });

        this.map.on('mouseleave', 'gangs', () => {
          this.map.getCanvas().style.cursor = '';
        });

    },

    createFilter: function(year) {

        return function (f) {
          return "properties" in f &&
                  'Year' in f['properties'] && f['properties']['Year'] === year;
      }
    },

    filterData: function() {
      let f = this.createFilter(this.filterYear);
      let filteredFeatures = this.origGeodata["features"].filter(f);
      console.log("filteredFeatures",filteredFeatures);
      let coordLookup = {};
      for (let i = 0; i < filteredFeatures.length; i++) {
        let coord = filteredFeatures[i].geometry.coordinates;
        if (coord in coordLookup) {
          coordLookup[coord]['properties'].push(filteredFeatures[i].properties);
        } else {
          coordLookup[coord] = {
            'coordinates': coord, 'properties': [filteredFeatures[i].properties],
            'col': filteredFeatures[i].properties.government
          };
        }
      }
      console.log("coordLookup",coordLookup);
      let features = [];
      for (let coord in coordLookup) {
        features.push({'geometry': {'coordinates': coordLookup[coord]['coordinates']},
          'properties': {'size': coordLookup[coord].properties[0]['Assigned'],
            'circlesize': Math.log(coordLookup[coord].properties[0]['Assigned'])*20,
          //'size': coordLookup[coord]['properties'].length,
          'data': coordLookup[coord].properties}});
      }
      console.log("features",features);
      let filteredGeodata = {"type" : "FeatureCollection", "features" : features };

      console.log("filteredGeodata",filteredGeodata);
      return filteredGeodata;
    },

    update: function(year) {
      // update the map filter
      // Year
      this.filterYear = year;

      // Update map
      this.map.getSource('gangsData').setData(this.filterData());

    }
  },
  mounted() {
    // load data
    let dataLoaded = data => {

      this.origGeodata = data;

      this.map.on('load', () => {
        this.addMapLayers();
      });
      this.map.on('style.load', () => {
        this.addMapLayers();
      });
      //console.log("origGeodata",this.origGeodata);
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
    font-family: 'Helvetica Neue', Helvetica, Arial, Sans-serif,serif;
  }

  #map {
    position: absolute;
    top: 0;
    bottom: 0;
    width: 100%;
  }

  h1 {
    font-size: 30px;
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

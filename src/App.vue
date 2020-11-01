<template>
  <div id="app">
    <div id="map"></div>
    <control @update="update"></control>
    <info :conInfo="conInfo"></info>

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
    info,

  },
  data() {
    return {
      map: null,
      accessToken: 'pk.eyJ1IjoibW9uYWxlbmEiLCJhIjoiY2l6dzFuenBzMDFvYjMyazdhcWMwd2dsMCJ9.xlKLbnrGSgAjxHLOM6JbVw',
      origGeodata:{},
      filterYear: 1835,
      filterGender: 0,
      // Create a popup, but don't add it to the map yet.
      popup: new mapboxgl.Popup({
        closeButton: false,
        closeOnClick: false
      }),
      conInfo: {},
    }
  },
  methods: {
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
      let filteredGeodata = {"type" : "FeatureCollection", "features" : this.origGeodata["features"].filter(f) };
      let coordLookup = {};
      for (let i = 0; i < filteredGeodata.features.length; i++) {
        let coord = filteredGeodata.features[i].geometry.coordinates;
        if (coord in coordLookup) {
          coordLookup[coord].push(i);
        } else {
          coordLookup[coord] = [i];
        }
      }
      filteredGeodata['coordLookup'] = coordLookup;
      console.log(filteredGeodata);
      return filteredGeodata;
    }
  },
  mounted() {
    // load data
    var dataLoaded = data => {
      this.origGeodata = data;

      // console.log(this.origGeodata);
      // console.log(this.origGeodata.features[0].geometry.coordinates);
      // console.log(this.origGeodata.features[0].properties);



      console.log(this.coordLookup);
      // https://docs.mapbox.com/help/tutorials/show-changes-over-time/#create-a-slider-bar
      this.map.on('load', () => {
        this.map.addSource('absconderData', {
          type: 'geojson',
          data: this.filterData(),
          cluster: true,
          clusterMaxZoom: 14, // Max zoom to cluster points on
          clusterRadius: 50
        });

        // this.map.addLayer({
        //   id: 'clusters',
        //   type: 'circle',
        //   source: 'absconderData',
        //   filter: ['has', 'point_count'],
        //   paint: {
        //     // Use step expressions (https://docs.mapbox.com/mapbox-gl-js/style-spec/#expressions-step)
        //     // with three steps to implement three types of circles:
        //     //   * Blue, 20px circles when point count is less than 100
        //     //   * Yellow, 30px circles when point count is between 100 and 750
        //     //   * Pink, 40px circles when point count is greater than or equal to 750
        //     'circle-color': [
        //       'step',
        //       ['get', 'point_count'],
        //       '#25707f',
        //       10,
        //       '#fed443',
        //       25,
        //       '#9d2338'
        //     ],
        //     'circle-radius': [
        //       'step',
        //       ['get', 'point_count'],
        //       20,
        //       100,
        //       30,
        //       750,
        //       40
        //     ]
        //   }
        // });
        //
        // this.map.addLayer({
        //   id: 'cluster-count',
        //   type: 'symbol',
        //   source: 'absconderData',
        //   filter: ['has', 'point_count'],
        //   layout: {
        //     'text-field': '{point_count_abbreviated}',
        //     'text-font': ['DIN Offc Pro Medium', 'Arial Unicode MS Bold'],
        //     'text-size': 12
        //   }
        // });
        //
        // this.map.addLayer({
        //   id: 'unclustered-point',
        //   type: 'circle',
        //   source: 'absconderData',
        //   filter: ['!', ['has', 'point_count']],
        //   paint: {
        //     'circle-color': '#25707f',
        //     'circle-radius': 4,
        //     'circle-stroke-width': 1,
        //     'circle-stroke-color': '#fff'
        //   },
        // });


        this.map.addLayer({
          id: 'absconders',
          type: 'circle',
          source: 'absconderData',
          paint: {
            'circle-color': '#25707f',
            'circle-radius': 4,
            'circle-stroke-width': 1,
            'circle-stroke-color': '#fff'
          },
        });


        // https://docs.mapbox.com/mapbox-gl-js/example/popup-on-hover/
        this.map.on('mouseenter', 'unclustered-point', (e) => {
          // Change the cursor style as a UI indicator.
          this.map.getCanvas().style.cursor = 'pointer';
          //console.log(e.features);

          var coordinates = e.features[0].geometry.coordinates.slice();
          var forename = e.features[0].properties.Forename;
          var surname = e.features[0].properties.Surname;
          var description = e.features[0].properties.Description;
          var masterTitle = e.features[0].properties.MasterTitle;
          var masterGiven = e.features[0].properties.MasterGiven;
          var masterFamily = e.features[0].properties.MasterFamily;
          var masterSuffix = e.features[0].properties.MasterSuffix;
          var accuracy = e.features[0].properties.Accuracy;
          var provenance = e.features[0].properties.Provenance;

          this.conInfo = {Forename: forename, Surname: surname, Description: description,
            MasterTitle: masterTitle, MasterGiven: masterGiven, MasterFamily: masterFamily,
            MasterSuffix: masterSuffix, Accuracy: accuracy, Provenance: provenance}


          // Ensure that if the map is zoomed out such that multiple
          // copies of the feature are visible, the popup appears
          // over the copy being pointed to.
          while (Math.abs(e.lngLat.lng - coordinates[0]) > 180) {
            coordinates[0] += e.lngLat.lng > coordinates[0] ? 360 : -360;
          }

          // Populate the popup and set its coordinates
          // based on the feature found.
          this.popup
                  .setLngLat(coordinates)
                  .setHTML(forename + ' ' + surname +
                          '<br>Description: ' + description +
                          '<br>Master: ' + masterTitle + ' ' + masterGiven + ' ' + masterFamily + ' ' + masterSuffix +
                          '<br>Accuracy: ' + accuracy + '<br>Provenance: ' + provenance)
                  .addTo(this.map);
        });

        this.map.on('mouseleave', 'unclustered-point', () => {
          this.map.getCanvas().style.cursor = '';
          this.popup.remove();
        });

        this.map.on('mouseenter', 'clusters', (e) => {
          // Change the cursor style as a UI indicator.
          this.map.getCanvas().style.cursor = 'pointer';
          console.log(e.features);
        });

        this.map.on('mouseleave', 'clusters', () => {
          this.map.getCanvas().style.cursor = '';
          this.popup.remove();
        });
      });
    }

    // load map
    mapboxgl.accessToken = this.accessToken;

    this.map = new mapboxgl.Map({
      container: 'map', // container id
      style: 'mapbox://styles/monalena/ck93lkfz50h1g1ipiaut42uhw', // stylesheet location
      center: [147.442,-42.804], // starting position [lng, lat]
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

  #console {
    position: absolute;
    width: 240px;
    margin: 10px;
    padding: 10px 20px;
    background-color: #fefcf6;
  }

  #session {
    margin-bottom: 20px;
  }

  .row {
    height: 12px;
    width: 100%;
  }

  .colors {
    background: linear-gradient(to right, #2dc4b2, #3bb3c3, #669ec4, #8b88b6, #a2719b, #aa5e79);
    margin-bottom: 5px;
  }

  .label {
    width: 15%;
    display: inline-block;
    text-align: center;
  }

  .mapboxgl-popup {
    max-width: 400px;
    font: 12px/20px 'Helvetica Neue', Arial, Helvetica, sans-serif;
  }
  .mapboxgl-popup-content {
    background: #fefcf6;
  }
</style>

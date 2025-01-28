<template>
    <div id='console'>
      <h1>Van Diemen's Land</h1>
      <h2>Road Gangs</h2>

        <div class='session' id='sliderbar'>
            <h3>in the Year <label id='active-year'>{{year}}</label></h3>
            <input id='slider' class='row' type='range' min='1829' max='1839' step='1' v-model='year'
            @input="$emit('update',parseInt(year), selected)">
        </div>
      <br>
      <div>Use the slider to select a year.</div>

      <br>
      <div>
        <Explanation></Explanation>
      </div>

    </div>
</template>


<script>
import Explanation from './Explanation.vue'

export default {
    name: "controlPanel",
  components: {
    Explanation
  },
    data: function() {
        return {
            year: null,
            selected: null,
            projection: null,
            projections: [
                {label: "Frankland", choice: "frank"}
            ]
        }
    },
    mounted: function() {
        this.year = 1829;
        this.selected = "all";
        this.projection = "frank";
    }
}
</script>

<style scoped>
    #console {
        position: absolute;
        width: 240px;
        margin: 10px;
        padding: 10px 20px;
        background-color: #fefcf6;
    }

    h1 {
        font-size: 26px;
        line-height: 28px;
        margin-bottom: 10px;
    }

    h2 {
        font-size:30px;
        line-height: 30px;

    }

    h3 {
      font-size: 18px;
      line-height: 20px;

    }

    a {
        text-decoration: none;
        color: #2dc4b2;
    }

    .row {
        height: 12px;
        width: 100%;
    }
</style>

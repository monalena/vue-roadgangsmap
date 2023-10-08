<template>
    <div id='console'>
        <h1>Absconder Map</h1>
        <Explanation></Explanation>
        <div class='session' id='sliderbar'>
            <h2>Year: <label id='active-year'>{{year}}</label></h2>
            <input id='slider' class='row' type='range' min='1818' max='1860' step='1' v-model='year'
            @input="$emit('update',parseInt(year), selected)">
        </div>
        <div class='session'>
            <h2>Gender</h2>
            <div v-for="item in filters" :key="item.label">
                <input :id='item.label' type='radio' v-model='selected' :value='item.choice'
                @change="$emit('update', parseInt(year), selected)">
                <label :for='item.label'>{{item.label}}</label>
            </div>
        </div>
        <div class="'projection">
            <h2>Projection</h2>
            <div v-for="item in projections" :key="item.label">
                <input :id="item.label" type="radio" v-model="projection" :value="item.choice"
                @change="$emit('changeProjection', projection)">
                <label :for="item.label">{{item.label}}</label>
            </div>
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
            filters: [
                {label: "All", choice: "all"},
                {label: "Female", choice: "female"},
                {label: "Male", choice: "male"}
            ],
            projections: [
                {label: "Frankland", choice: "frank"},
                {label: "Satellite", choice: "satellite"}
            ]
        }
    },
    mounted: function() {
        this.year = 1835;
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

    .row {
        height: 12px;
        width: 100%;
    }
</style>

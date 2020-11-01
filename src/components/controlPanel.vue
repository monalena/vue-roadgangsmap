<template>
    <div id='console'>
        <h1>Absconding Convicts</h1>
        <p>Data: <a href='data.geojson'>Absconding Notes with Georeferences</a> in VDL</p>
        <div class='session' id='sliderbar'>
            <h2>Year: <label id='active-year'>{{year}}</label></h2>
            <input id='slider' class='row' type='range' min='1810' max='1860' step='1' v-model='year'
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
    </div>
</template>


<script>
    export default {
        name: "controlPanel",
        data: function() {
            return {
                year: null,
                selected: null,
                filters: [
                    {label: "All", choice: "all"},
                    {label: "Female", choice: "female"},
                    {label: "Male", choice: "male"}
                ]
            }
        },
        mounted: function() {
            this.year = 1835;
            this.selected = "all";
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

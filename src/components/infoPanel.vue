<template>
    <div id="cards">
        <h1>{{info}}</h1>
        <div>
            <div v-if="!!conInfo[0].Location"><b>Location</b>: {{conInfo[0].Location}}</div>
            <div v-if="!!conInfo[0].Town"><b>Place</b>: {{conInfo[0].Town}}</div>
            <div v-if="!!conInfo[0].Accuracy"><b>Accuracy</b>: {{conInfo[0].Accuracy}}</div>
            <div v-if="!!conInfo[0].Provenance"><b>Provenance</b>: {{conInfo[0].Provenance}}</div>
            <div> </div>
        </div>
        <div v-for="(item,i) in orderedConInfo" :key="i">
            <div class="card">
                <div class="card-divider">
                    <p class="card-header">
                        Name: {{item.Forename}} {{item.Surname}}

                    </p>
                </div>
                <div class="card-section">
                    <div>Date: {{item.Date}}</div>
                    <div>Description: {{item.Description}}</div>
                    <div v-if="!!item.MasterFamily">Master: {{item.MasterTitle}} {{item.MasterGiven}}  {{item.MasterFamily}} {{item.MasterSuffix}}</div>
                </div>
            </div>
        </div>
    </div>

</template>

<script>
    import _ from 'lodash';
    export default {
        name: "infoPanel",
        props:["conInfo"],
        data: function() {
            return {
                info: "Details",
            }
        },
        methods: {
            // fillCards: function(conInfo) {
            //     console.log('Info:', conInfo);
            // }
        },
        computed: {
            orderedConInfo: function () {
                let sortedArray = _.orderBy(this.conInfo, function(dateObj) {
                    return new Date(dateObj.newDate);
                });
                return sortedArray;
            }
        }
    }
</script>

<style scoped>

    #cards {
        position: absolute;
        width: 240px;
        margin-top: 380px;
        margin-left: 10px;
        padding: 10px 20px;
        background-color: #fefcf6;
        overflow-y: scroll;
        max-height: 50%;
    }

    .card{
        background-color: gainsboro;
        padding: 2px;
    }

    .card-header{
        font-weight: bold;
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

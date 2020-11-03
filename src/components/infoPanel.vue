<template>
    <div id="cards">
        <h1>{{info}}</h1>
        <div v-for="(item,i) in conInfo" :key="i">
            <div class="card">
                <div class="card-divider">
                    <p class="card-header">
                        Name: {{item.Forename}} {{item.Surname}}

                    </p>
                </div>
                <div class="card-section">
                    <p>
                        Date: {{item.Date}}
                        <br>Description: {{item.Description}}
                        <br>Master: {{item.MasterTitle}} {{item.MasterGiven}}  {{item.MasterFamily}} {{item.MasterSuffix}}
                        <br>Place: {{item.Town}}
                        <br>Accuracy: {{item.Accuracy}}
                        <br>Provenance: {{item.Provenance}}
                    </p>
                </div>
            </div>
        </div>
    </div>

</template>

<script>
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

<template>
    <div id="cards">
        <h1>{{info}}</h1>
        <div class="card">
            <div class="card-divider">
                <p class="card-header">
                    Name: {{conInfo.forename}} {{conInfo.surname}}
                </p>
            </div>
            <div class="card-section">
                <p>
                    Description:
                    <br>Master: masterTitle masterGiven  masterFamily masterSuffix
                    <br>Accuracy:
                    <br>Provenance:
                </p>
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
        }
    }
</script>

<style scoped>

    #cards {
        position: absolute;
        width: 240px;
        margin-top: 300px;
        margin-left: 10px;
        padding: 10px 20px;
        background-color: #fefcf6;
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

    .row {
        height: 12px;
        width: 100%;
    }

</style>

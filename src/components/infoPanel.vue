<template>
    <div id="cards">
        <div>
          <div><b>Government Location</b>: {{info[0].Place}} {{info[0].cleanProperty}}</div>
          <div v-if="!!info[0].District"><b>District</b>: {{info[0].District}}</div>
          <div v-if="!!info[0].County"><b>County</b>: {{info[0].County}}</div>
          <div><b>Assigned</b>: {{info[0].Assigned}}</div>
        </div>
    </div>

</template>

<script>


    export default {
        name: "infoPanel",
        props:["info", "origGeoData"],
        data: function() {

        }
    }
</script>

<style scoped>

    #cards {
      position: absolute;
      width: 240px;
      margin-top: 282px;
      margin-left: 10px;
      padding: 10px 20px;
      background-color: #fefcf6;
      max-height: 50%;
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

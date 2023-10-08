<template>
    <div id="cards">
      <div>
        <h1 @mouseover="hover=true" @mouseleave="hover=false">{{info}}</h1>
        <transition name="bounce">
          <div class="explain" v-if="hover">{{ explain }}</div>
        </transition>
      </div>

        <div>
          <div v-if="!!conInfo[0].ConvictPlace"><b>Government Location</b>: {{conInfo[0].ConvictPlace}}</div>
          <div v-else><b>Location</b>: {{conInfo[0].Location}}</div>
          <div v-if="!!conInfo[0].Accuracy"><b>Accuracy</b>: {{conInfo[0].Accuracy}}</div>
          <!--div v-if="!!conInfo[0].Provenance"><b>Provenance</b>: {{conInfo[0].Provenance}}</div-->
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
                  <div>Ship: {{item.Ship}}</div>
                  <div>Gazetted: {{item.Date}}</div>
                    <div>Absconded from: {{item.Description}}</div>
                    <!--div v-if="!!item.MasterFamily">Master: {{item.MasterTitle}} {{item.MasterGiven}}  {{item.MasterFamily}} {{item.MasterSuffix}}</div-->
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
              explain: "Get more details on individual abscondings by clicking on the circles in the map.",
              hover: false,
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

    .explain{
      position: absolute;
      z-index: 10;
      top:50px;
      right:10px;
      display: block;
      visibility: visible;
      width: 190px;
      padding: 1rem;
      border: 1px solid #cacaca;
      border-radius: 0;
      background-color: #fefcf6;
      font-size: 1rem;
    }


    .bounce-enter-active {
      animation: bounce-in .5s;
    }
    .bounce-leave-active {
      animation: bounce-in .5s reverse;
    }
    @keyframes bounce-in {
      0% {
        transform: scale(0);
      }
      50% {
        transform: scale(1.1);
      }
      100% {
        transform: scale(1);
      }
    }


</style>

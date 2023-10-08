
<template>
  <div>
    <button class="button float-right secondary small" type="button" @mouseover="showDropDown=true" @mouseout="showDropDown=false">
      Background Information
    </button>
    <transition name="bounce">
      <div v-if="showDropDown" class="dropdown-pane bottom" >{{information}}</div>
    </transition>
  </div>
</template>

<script>
/* eslint-disable */

  export default {
    name: "info",
    components: {},
      data: function() {
        return {
          showDropDown: false,
          information: 'The Absconder map is based on the Absconding notices published in the Hobart Town Gazette between 1818 and 1860. ' +
              'Georeferencing has been done by Trudy  Cowley, ' +
              'and includes references made by Richard Tuffin, as part  of the research project Landscapes of Production and Punishment. ' +
              'The Absconder map has been created by Monika Schwarz as part of the ARC funded Conviction Politics Project.'
        }
      },
      mounted: function() {
      },
      destroyed: function() {
      }

    }
</script>
<style scoped>
    .dropdown-pane{
        position: absolute;
        z-index: 10;
        top:66px;
        right:-335px;
        display: block;
        visibility: visible;
        width: 400px;
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

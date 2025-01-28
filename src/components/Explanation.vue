
<template>
  <div>
    <button class="button float-right secondary small" type="button" @mouseover="showDropDown=true" @mouseout="showDropDown=false">
      Explanation
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
          information: 'Visualised are assignments to Tasmanian road gangs, road stations, penal stations and similar between the years 1829-1839.'
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
        top:190px;
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

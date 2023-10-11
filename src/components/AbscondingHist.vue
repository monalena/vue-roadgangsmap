<template>
  <div id='AbsconderHisto'>
    <div class="row">
      <div class="left-element">
        <h1>Absconding History</h1>
      </div>
      <div class="right-element">
        <b-icon-x-lg class="top-bar-left" @click="clicked"></b-icon-x-lg>
      </div>
    </div>
    <table id="tableComponent">
      <thead>
      <tr>
        <th>Year</th>
        <th>Gazetted</th>
        <th>Description</th>
      </tr>
      </thead>
      <tbody class="cells">
      <!-- Loop through the list get the each student data -->
      <tr v-for="(item,i) in orderedAbsHist" :key='i' >
        <th>{{item.year}}</th>
        <th>{{item.date.toDateString()}}</th>
        <th>{{item.description}}</th>
      </tr>
      </tbody>
    </table>

  </div>
</template>

<script setup>
import _ from "lodash";

export default {
  name: "abscondingHistory",
  props:["abscondingHistory"],
  data() {
    return {
/*      isVisible: true,*/
    }
  },
  methods: {
/*    closeAbsconderHisto() {
      this.isVisible = false; // Close the component
    },*/
    clicked() {
      this.$emit('clicked')
    }
  },
  computed: {
    orderedAbsHist: function () {
      let sortedArray = _.orderBy(this.abscondingHistory, function(dateObj) {
        return new Date(dateObj.date);
      });
      return sortedArray;
    }
  }

}

</script>

<style scoped>

#AbsconderHisto {
  position: absolute;
  width: 400px;
  margin-top: 380px;
  margin-left: 300px;
  padding: 10px 20px;
  background-color: #fefcf6;
}
.cells {
  font-size:14px;
  font-style: normal;
  font-weight: inherit;
}
.row {
  display: flex;
  justify-content: space-between; /* Distribute items to the left and right */
  align-items: center; /* Vertically align items */
}

.left-element {
  flex: 1; /* Grow to fill available space (left element) */
}

.right-element {
  flex: 1; /* Grow to fill available space (right element) */
  text-align: right; /* Align content to the right */
}

thead {
  font-weight: bold;
}

th {
  font-weight: inherit;
}

h1 {
  font-size: 20px;
  line-height: 30px;
}

tr:hover {background-color: gainsboro;}

</style>
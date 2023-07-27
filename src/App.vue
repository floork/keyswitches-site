<template>
  <div class="container">
    <h1>Switch Types and Producers</h1>
    <div class="search-bar">
      <input type="text" v-model="searchQuery" @input="performSearch" placeholder="Search anything" class="search-input">
    </div>
    <div class="table-container">
      <table>
        <tr class="fixed-header">
          <th>Model</th>
          <th>Feel</th>
          <th>Actuation</th>
          <th>Pre-Travel</th>
          <th>Travel</th>
          <th>Mount</th>
        </tr>
        <tr v-for="(switchData, index) in filteredSwitches" :key="index">
          <td>{{ switchData.model }}</td>
          <td>{{ switchData.feel }}</td>
          <td>{{ switchData.actuation }}</td>
          <td>{{ switchData.preTravel }}</td>
          <td>{{ switchData.totalTravel }}</td>
          <td>{{ switchData.mount }}</td>
        </tr>
      </table>
    </div>
  </div>
  <div class="bubbles">
    <div class="bubble" v-for="i in 10" :key="i"></div>
  </div>
</template>

<script setup lang="ts">
import Fuse from 'fuse.js';
import { onMounted, ref } from 'vue';
import jsondata from './switches.json';

class Switch {
  constructor(model, feel, actuation, preTravel, totalTravel, mount) {
    this.model = model;
    this.feel = feel;
    this.actuation = actuation;
    this.preTravel = preTravel;
    this.totalTravel = totalTravel;
    this.mount = mount;
  }
}

const switches = ref([]);
const searchQuery = ref('');
const filteredSwitches = ref([]);
const options = {
  keys: ['model', 'feel', 'actuation', 'preTravel', 'totalTravel', 'mount'],
  threshold: 0.3, // Adjust the threshold for fuzzy matching
};

function getData(){
  switches.value = jsondata.map((data) => new Switch(data.model, data.feel, data.actuation, data.preTravel, data.totalTravel, data.mount));
}

function customSearch(query) {
  const fuse = new Fuse(switches.value, options);
  return fuse.search(query).map(result => result.item);
}

function performSearch() {
  const query = searchQuery.value;
  filteredSwitches.value = query ? customSearch(query) : switches.value;
}

onMounted(() => {
  getData();
  performSearch();
});

</script>


<style lang="scss" >
body {
  background: #1a1e23;
  margin: 0;
}

$bubble-count: 50;
$sway-type: "sway-left-to-right", "sway-right-to-left";

@function random_range($min, $max) {
  $rand: random();
  $random_range: $min +floor($rand * (($max - $min) + 1));
  @return $random_range;
}

@function sample($list) {
  @return nth($list, random(length($list)));
}

.bubbles {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100vh;
}

.bubble {
  position: absolute;
  left: var(--bubble-left-offset);
  bottom: -75%;
  display: block;
  width: var(--bubble-radius);
  height: var(--bubble-radius);
  border-radius: 50%;
  animation: float-up var(--bubble-float-duration) var(--bubble-float-delay) ease-in infinite;

  &::before {
    position: absolute;
    content: '';
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: hsla(183, 94%, 76%, 0.3);
    border-radius: inherit;
    animation: var(--bubble-sway-type) var(--bubble-sway-duration) var(--bubble-sway-delay) ease-in-out alternate infinite;
  }

  @for $i from 0 through $bubble-count {
    &:nth-child(#{$i}) {
      --bubble-left-offset: #{random_range(0vw, 100vw)};
      --bubble-radius: #{random_range(1vw, 10vw)};
      --bubble-float-duration: #{random_range(6s, 12s)};
      --bubble-sway-duration: #{random_range(4s, 6s)};
      --bubble-float-delay: #{random_range(0s, 4s)};
      --bubble-sway-delay: #{random_range(0s, 4s)};
      --bubble-sway-type: #{sample($sway-type)};
    }
  }
}

@keyframes float-up {
  to {
    transform: translateY(-175vh);
  }
}

@keyframes sway-left-to-right {
  from {
    transform: translateX(-100%);
  }

  to {
    transform: translateX(100%);
  }
}

@keyframes sway-right-to-left {
  from {
    transform: translateX(100%);
  }

  to {
    transform: translateX(-100%);
  }
}

.container {
  z-index: 2;
  position: absolute;
  margin-left: 33%;
}

body,
h1,
p,
table {
  margin: 0;
  padding: 0;
}

/* Style the heading */
h1 {
  text-align: center;
  margin: 20px 0;
  color: #beb5b5;
}

/* Style the search bar */
.search-bar {
  display: flex;
  justify-content: center;
  margin: 20px auto;
  max-width: 500px;
}

.search-input {
  padding: 10px;
  border: 2px solid #ccc;
  border-radius: 5px;
  width: 100%;
  max-width: 300px;
}

/* Style the table */
.table-container {
  margin: 20px auto;
  max-width: 800px;
}

table {
  width: 100%;
  border-collapse: collapse;
  background-color: rgba(255, 255, 255, 0.8);
  /* Add some transparency to the table background */
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

th,
td {
  padding: 10px;
  text-align: left;
  color: #333;
  /* Set the table text color to a darker shade to improve visibility */
}

th {
  background-color: #f2f2f2;
}

tr:nth-child(even) {
  background-color: #f9f9f9;
}

/* Add some hover effect on table rows */
tr:hover {
  background-color: #e5e5e5;
}

/* Style the table header when it's fixed */
.fixed-header th {
  position: sticky;
  top: 0;
  background-color: #f2f2f2;
  z-index: 1;
}
</style>

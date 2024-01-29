<template>
  <section>
    <h1>Ronslist</h1>
    <ul>
        <li v-for="(part, index) in this.parts" :key="index" :class="[`${index}`]">{{part["Fastener Type"]}}
            <ul>
                <li v-for="(specValue, specKey) in part" :key="specValue['Web Link']">{{specKey}}:{{specValue}}</li>
            </ul>
        </li>
    </ul>
  </section>
</template>




<script>
// Importing array from our JSON file
import json from "@/assets/fasteners.json";

export default {
  name: "PartsDirectory",
  data: function() {
    return {
      parts: json // passing array data into Vue
    };
  },
// this is how you filter, just need to loop through all the values of each property and create like a dropdown for each or something
//then you render the list with the computed property, not the base data
//although I think you should actually use a method here?
//then you pass the variable via v-on:click="filterItems(etc etc)""
//   computed: {
//     filterItems() {
//       return data.filter(obj => {
//             return obj.[key] === value;
//         })
//     }
//   }
};
</script>
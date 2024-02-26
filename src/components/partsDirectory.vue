<template>
  <section>
    <h1>Ronslist</h1>
    <input type="text" v-model="searchQuery">
    <div class="fieldset-wrapper">
      <fieldset v-for="filter in this.filters" :key="filter">
        <legend>{{ filter }}</legend>      
        <div v-for="filterValue in this.filterValues[filter]" :key="filterValue" @click="setActiveFilters(filter, filterValue)">
          <input type="checkbox" :id="filterValue" :name="filterValue">
          <label>{{ filterValue }}</label>
        </div>
      </fieldset>
    </div>
    <ul>
        <li v-for="part in searchItems" :key="part['MFG PN']">{{part["Manufacturer"] + " " + part["MFG PN"]}}
          {{ console.log(part.item) }}
            <ul>
                <li v-for="(specValue, specKey) in part" :key="specValue['Web Link']">{{specKey}}: {{specValue}}</li>
            </ul>
        </li>
    </ul>
  </section>
</template>

<script>

//need to build in and/or logic for filter groups, add additional control that tells it which json to render. 
//hard code the top-level keys for each item group
//consider the experience of revealing additional filters as you narrow your search


// Importing array from our JSON file
import Fuse from 'fuse.js';
import json from "@/assets/fasteners.json";

export default {
  name: "PartsDirectory",
  data: function() {
    return {
      parts: json, // passing array data into Vue
      filters: [
        "Primary Use",
        "Manufacturer",
        "Head Type"
      ],
      filterValues: {},
      activeFilters: {},
      searchQuery: ''
    };
  },
  mounted(){
      this.filters.forEach(filter => {
        let listOfValues = []
        this.parts.forEach(part => {
          listOfValues.push(part[filter])
        })
        this.filterValues[filter] = [...new Set(listOfValues)]
        this.activeFilters[filter] = [];
      })

      const fuseOptions = {
        isCaseSensitive: false,
        includeScore: false,
        // shouldSort: true,
        includeMatches: false,
        findAllMatches: false,
        // minMatchCharLength: 1,
        // location: 0,
        threshold: 0.2,
        // distance: 100,
        // useExtendedSearch: false,
        // ignoreLocation: false,
        // ignoreFieldNorm: false,
        // fieldNormWeight: 1,
        keys: Object.keys(this.parts[0])
      };

      this.fuse = new Fuse(this.filterItems, fuseOptions)
      return undefined;
    },
  computed: {
    filterItems(){
      let filteredItems = [];
      let filtersApplied = 0;
      for (let activeFilterCat in this.activeFilters) {
        filtersApplied += this.activeFilters[activeFilterCat].length;
      }
      if (filtersApplied > 0) {
        this.parts.forEach(part => {
          for (let activeFilterCat in this.activeFilters) {
            this.activeFilters[activeFilterCat].forEach(activeFilter => {
              if (Object.values(part).includes(activeFilter)) {
                filteredItems.push(part)
              }
            })
          }
        })
        return [...new Set(filteredItems)];
        } else {
          return this.parts;
      }
    },
    searchItems(){
        if (this.searchQuery.length > 2) {
        let results = this.fuse.search(this.searchQuery);
        console.log(results)
        let newResults = []
        results.forEach(result =>{newResults.push(result.item)})//fuse returning weird structure, this fixes temporarily
        return newResults
        } else {
          return this.filterItems;
      }
    }
  },
  methods: {
    setActiveFilters(filterCat, filterValue) {
      this.filtered = true;
      if (!this.activeFilters[filterCat].includes(filterValue)) {
          this.activeFilters[filterCat].push(filterValue)
        } else {
          let index = this.activeFilters[filterCat].indexOf(filterValue);
            if (index > -1) { // only splice array when item is found
            this.activeFilters[filterCat].splice(index, 1); // 2nd parameter means remove one item only
          }
        }
      }
    },
  }
</script>

<style>

.fieldset-wrapper {
  display: flex;
  flex-direction: row;
}

ul ul {
  margin-bottom: 20px;
}

.filters {
  list-style: none;
  padding: 0;
}

.active {
  color: red;
}

</style>
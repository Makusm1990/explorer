<template>
<div>
  <div class="card"> 
    <v-expansion-panels>
      <v-expansion-panel>
        <v-expansion-panel-header v-if="blockData">
          <h2>Top Farmers in the last 1000 Blocks  </h2>
          <h2>Range: {{StartHeight.height}} - {{EndHeight.height}}</h2>
        </v-expansion-panel-header>
      <v-expansion-panel-content>
        <div class="card flex">
          <v-data-table  
            :headers="headers"
            :items="blockData"
            :items-per-page="itemsPerPage"
            class="elevation-1 text-left"
            hide-default-footer>
            <template v-slot:item.farmer_puzzle_hash="{item}">
              <nuxt-link :to="'/wallet/' + item.farmer_puzzle_hash">
                {{item.farmer_puzzle_hash}}  
              </nuxt-link>
            </template>   
          </v-data-table> 
        </div>
        </v-expansion-panel-content>
    </v-expansion-panel>
  </v-expansion-panels>
  </div>
  <div class="card"> 
    <v-expansion-panels>
      <v-expansion-panel>
        <v-expansion-panel-header v-if="blockData">
          <h2>Stai Plot size distribution</h2>
        </v-expansion-panel-header>
      <v-expansion-panel-content>
        <div class="card flex" v-if="blockData">
          <h2>K32: {{k32Plots[0].count}}</h2>
        </div>
        <div class="card flex" v-if="blockData">
          <h2>K33: {{k32Plots[1].count}}</h2>
        </div>
        <div class="card flex" v-if="blockData">
          <h2>K34: {{k32Plots[2].count}}</h2>
        </div>
        <div class="card flex" v-if="blockData">
          <h2>K35: {{k32Plots[3].count}}</h2>
        </div>
        <div class="card flex" v-if="blockData">
          <h2>K36: {{k32Plots[4].count}}</h2>
        </div>
        <div class="card flex" v-if="blockData">
          <h2>K37: {{k32Plots[5].count}}</h2>
        </div>
        <div class="card flex" v-if="blockData">
          <h2>K38: {{k32Plots[6].count}}</h2>
        </div>
        </v-expansion-panel-content>
    </v-expansion-panel>
  </v-expansion-panels>
  </div>
</div>
  
</template>


<script>
export default { 
  data() {
    return{
      blockData: null,
      StartHeight: null,
      EndHeight: null,
      k32Plots: null,
      itemsPerPage: 50,
      headers: [
        {
          align: 'center',
        },
        {text: "Farmer Address", value: "farmer_puzzle_hash", sortable: false},
        {text: "Won", value: "wins", sortable: false},
      ],
    };
  },
  async fetch() {
    const fetchData = await fetch("http://localhost:8080/stai/statistics").then((res) => res.json())
    this.blockData = fetchData.Farmers
    this.StartHeight = fetchData.HeightStart
    this.EndHeight = fetchData.HeightEnd
    this.k32Plots = fetchData.Plots
  }, 

  methods: {

    formatTimestamp(item) {
      if (item.timestamp > 0)
      return new Date(item.timestamp*1000).toUTCString()
    },
    formatType(item) {
      if (item.timestamp > 0)
      return "TX - Block"
      else
      return "Block"
    },
  },
  }
</script>


<style scoped>
a {
  text-decoration: none;
  color: #95BC0E;
}
</style>
<template>
  <div>
    <div class="header-cards card-deck">
      <div class="row">
        <div class="card flex">
          <div class="card-body h2">
            <h2 class="card-title" v-if="currentHeight">Height</h2>
              {{currentHeight}}
          </div>
        </div>
        <div class="card flex">
          <div class="card-body h2">
            <h2 class="card-title" v-if="netspace">Netspace</h2>
            {{netspace}} EiB
          </div>
        </div>
        <div class="card flex">
          <div class="card-body h2">
            <h2 class="card-title" v-if="difficulty">Difficulty</h2>
            {{difficulty}}
          </div>
        </div>
        <div class="card flex">
          <div class="card-body h2">
            <h2 class="card-title">Unique addresses</h2>
            {{unique_addresses}}
          </div>
        </div>
        <div class="card flex">
          <div class="card-body h2">
            <h2 class="card-title" v-if="totalCoins">Total coin supply</h2>
            {{totalCoins}}
          </div>
        </div>
      </div>
    </div> 
    <div class="text-center ">
      <div class="card">
        <h1>Blockhistory</h1>
        <v-pagination
          v-model="currentPage"
          :length="totalPages"
          :total-visible="10">
        </v-pagination>        
      </div>
    </div>
    <div class="card flex">
      <v-data-table  
        :headers="headers"
        :items="blocks"
        :items-per-page="itemsPerPage"
        :currentPage.sync="currentPage"
        :server-items-length="blocks.numOfResults"
        :loading="loading"
        :pageCount = "currentPage"
        class="elevation-1 text-left"
        hide-default-footer>

        <template v-slot:item.height="{item}">
            {{item.height}}  
        </template>

        <template v-slot:item.type="{item}">
          <img src="logo.png" alt="" height=15 width=15>
          {{formatType(item)}}
        </template>
        <template v-slot:item.timestamp="{item}">
          {{formatTimestamp(item)}}
        </template>
        <template v-slot:item.header_hash_hax="{item}">
          <nuxt-link :to="'/block/' + item.header_hash_hax">
            {{item.header_hash_hax}}  
          </nuxt-link>
        </template>   
      </v-data-table> 
    </div>
    <div class="text-center">
      <div class="card">
        <v-pagination
          v-model="currentPage"
          :length="totalPages"
          :total-visible="10">
        </v-pagination>
      </div>
    </div>
  </div>
</template>


<script>
export default { 
  data() {
    return{
      totalBlocks: 0,
      loading: true,
      currentPage: 1,
      itemsPerPage: 50,
      totalPages: 0,
      unique_addresses: null,
      currentHeight: null,
      netspace: null,
      difficulty: null,
      totalCoins: null,
      blocks: [],
      headers: [
        {
          align: 'center',
        },
        {text: "Height", value: "height", sortable: false},
        {text: "Timestamp", value: "timestamp", sortable: false},
        {text: "Block hash", value: "header_hash_hax", sortable: false},
        {text: "Type", value: "type", sortable: false},
      ],
    };
  },
  async fetch() {
    await this.loadData();
  }, 
  watch: {
  currentPage() {
    this.loadData()
    this.loading = true
    }
  },

  methods: {
    async loadData() {
    const blockData = await fetch("http://localhost:8080/stai?page="+ this.currentPage).then((res) => res.json())
    this.blocks = blockData.block
    this.totalBlocks = blockData.numOfResults
    this.totalPages = blockData.numberOfPages
    this.currentPage = blockData.page
    this.unique_addresses = blockData.unique_addresses
    this.height = blockData.block[1-1].height 
    this.totalCoins = (blockData.totalCoins).toLocaleString('en-US');
    this.currentHeight = blockData.Height.height
    this.difficulty = blockData.block[0].weight - blockData.block[1].weight
    this.netspaceFetch = await fetch("https://api.alltheblocks.net/stai/chain").then((res) => res.json())
    this.netspace = (this.netspaceFetch.netspaceBytes/(1024**6)).toFixed(3)
    console.log((this.netspaceFetch.netspaceBytes/(1024**6)).toFixed(4))
    this.loading = false
    },

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

th {
  background-color: #95BC0E !important;
  font-style: normal;
  font-size:100% !important;
  }

.card {
  padding: 0.75rem;
  background-color: #384141;
  border: 1px solid #95BC0E;
  font-size: 1.35rem;
  text-align: center !important;
  margin: 0.5rem;
  margin-top: 1rem;
  margin-bottom: 1rem;
}
.card-deck {
    flex: 1 0 0%;
    margin-right: 15px;
    margin-bottom: 0;
    margin-left: 15px;
}

</style>
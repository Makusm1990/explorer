<template>
<div>
  <div class="card">
    <div class="text-center d-flex flex-column">
      <div> 
        <v-card v-if="walletData">
            <h1 class="pa-1">Wallet Information</h1>
          <h2 class="pa-1 mb-3 h2">{{staiAddress(walletData)}}</h2>
        </v-card>
      </div>
      
      <v-card class="text-left mb-1" v-if="walletData" elevation="-1">
        <v-card-title> Last win: </v-card-title>
        <v-card-subtitle>{{formatTimestamp_last_win(walletData)}}</v-card-subtitle>
      </v-card>

      <v-card class="text-left mb-1" v-if="walletData" elevation="-1">
        <v-card-title>Balance: </v-card-title>
        <v-card-subtitle>{{formatBalance(walletData)}} STAI</v-card-subtitle>
      </v-card> 

      <v-card class="text-left mb-1" elevation="-1">
        <v-card-title>Note: </v-card-title>
        <v-card-subtitle>This is one address, but your wallet can have multiple addresses, so this balance may not reflect everything in your wallet.</v-card-subtitle>
      </v-card>
    </div> 
  </div>
    
  <div class="card flex">
    <div >
      <h3>History</h3>       
    </div>
      <v-pagination
        v-model="currentPage"
        :length="totalPages"
        :total-visible="10">
      </v-pagination>
  </div>
        
      
    <div class="card flex">
      <v-data-table  
        :headers="headers"
        :items="coins"
        :items-per-page="itemsPerPage"
        :currentPage.sync="currentPage"
        :server-items-length="totalCoins"
        :loading="loading"
        :pageCount = "currentPage"
        class="elevation-1 text-left flex"
        hide-default-footer>

        <template v-slot:item.coin_name="{item}">
          <nuxt-link :to="'/coin/' + item.coin_name">
            {{item.coin_name}}  
          </nuxt-link>
        </template>

        <template v-slot:item.coinbase="{item}">
          <v-icon size="100%">{{item.coinbase == true ? 'mdi-barley' : 'mdi-account-convert'}}</v-icon>
          {{formatCoinbase(item)}}
        </template>

        <template v-slot:item.timestamp="{item}">
          {{formatTimestamp(item)}}
        </template>

        <template v-slot:item.amount="{item}">
          {{formatAmount(item)}}            
        </template>
      </v-data-table>
    </div>
      <div class="card">
        <v-pagination
          v-model="currentPage"
          :length="totalPages"
          :total-visible="10"
        ></v-pagination>
      </div>
    
</div>  
</template>


<script>
export default {
  data() {
    return{
      walletData: null,
      totalCoins: 0,
      loading: true,
      currentPage: 1,
      itemsPerPage: 50,
      totalPages: 0,
      coins: [],
      headers: [
        {text: "Height confirmed", value: "confirmed_index", sortable: false},
        {text: "Type", value: "coinbase", sortable: false},
        {text: "Coin Name", value: "coin_name", sortable: false},
        {text: "Amount", value: "amount", sortable: false},
        {text: "Height Spent", value: "spent_index", sortable: false},
        {text: "Timestamp", value: "timestamp", sortable: false},
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
    const data = await fetch("http://localhost:8080/stai/wallet/"+this.$route.params.wallet+"?page="+this.currentPage).then((res) => res.json())
    this.walletData = data
    this.coins = data.all_coins
    this.totalCoins = data.numOfResults
    this.totalPages = data.numberOfPages
    this.currentPage = data.page
    this.loading = false
    },

    formatCoinbase(item) {
      if (item.coinbase == true)
      return "Farmer Reward" 
      else
      return "Miscellaneous"
    }, 
    formatTimestamp(item) {
      if (item.timestamp > 0)
      return new Date(item.timestamp*1000).toUTCString()
    },
    formatAmount(item) {
      return(item.amount/1000000000).toFixed(9)
    },

    formatTimestamp_last_win(walletData) {
      if (walletData.last_win.timestamp > 0)
      return new Date(walletData.last_win.timestamp*1000).toUTCString()
    },
    formatType(walletData) {
      if (walletData.last_win.timestamp > 0)
      return "TX - Block"
      else
      return "Block"
    },
    formatBalance(walletData) {
      return(walletData.balance.sum/1000000000)
    },
    staiAddress(walletData) {
      return(walletData.last_win.puzzle_hash)

    }
  }
  }
</script>

<style scoped>
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
a {
  text-decoration: none;
  color: #95BC0E;
}

th {
  background-color: #96bc0e9a !important;
  font-style: normal;
  font-size:100% !important;
  }
.main_color {
  background-color: #384141;
  color:#ffffff !important;
}
h1 {
  display: block;
  font-size: 2.25rem;
  margin-block-start: 0.67em;
  margin-block-end: 0.67em;
  margin-inline-start: 0px;
  margin-inline-end: 0px;
  font-weight: bold;
  margin-top: 0;
  margin-bottom: 0.5rem;
}

h2 {
  margin-bottom: 0.5rem;
  font-weight: 500;
  line-height: 1.2;
  color: #95BC0E;
}

h3 {
  margin-bottom: 0.5rem;
}
</style>
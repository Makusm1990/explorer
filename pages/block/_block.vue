<template>
<div>
  <div class="card">
    <div class="card-header flex">
      <h1 class="h3 text-monospace mb-0" style="overflow:hidden;white-space:nowrap;text-overflow:ellipsis;">
        <v-icon size="100%">mdi-cube-outline</v-icon> {{blockData.header_hash_hax}}
      </h1>
    </div>
  </div>
  <div class=" flex">
    <table role="table" aria-busy="false" aria-colcount="11" class="table b-table table-sm b-table-stacked flex">
      <tbody role="rowgroup">
        <tr role="row" class>
          <td aria-colindex="1" data-label="Date:" role="cell" class>
            <div>
              {{formatTimestamp(blockData)}}
            </div>
          </td>
          <td aria-colindex="2" data-label="Height:" role="cell" class>
            <div>
                {{blockData.height}}
            </div>
          </td>
          <td aria-colindex="3" data-label="Weight:" role="cell" class>
            <div>
              {{blockData.weight}}
            </div>
          </td>
          <td aria-colindex="4" data-label="Total iterations:" role="cell" class>
            <div>
              {{blockData.total_iterations}}
            </div>
          </td>
          <td aria-colindex="5" data-label="Block iterations:" role="cell" class>
            <div>
              {{blockData.block_iterations}}
            </div>
          </td>
          <td aria-colindex="6" data-label="Proof of space size:" role="cell" class>
            <div>
              <v-icon size="100%">mdi-database</v-icon>
              K-{{blockData.proof_of_space_size}} Plot
            </div>
          </td>
          <td aria-colindex="7" data-label="Plot public key:" role="cell" class>
            <div>
              {{blockData.plot_public_key_hex}}
            </div>
          </td>
          <td aria-colindex="8" data-label="Pool public key:" role="cell" class>
            <div>
              {{blockData.pool_public_key_hex}}
            </div>
          </td>
          <td aria-colindex="9" data-label="Farmer puzzle hash:" role="cell" class>
            <div>
              <v-icon size="100%">mdi-barley</v-icon>
              <nuxt-link :to="'/wallet/' + blockData.farmer_puzzle_hash">
                {{blockData.farmer_puzzle_hash}}
              </nuxt-link>
            </div>
          </td>
          <td aria-colindex="10" data-label="Pool puzzle hash:" role="cell" class>
            <div>
              {{blockData.pool_puzzle_hash}}
            </div>
          </td>
          <td aria-colindex="11" data-label="Prev header hash hex:" role="cell" class>
            <div>
              <v-icon size="100%">mdi-cube-outline</v-icon>
              <nuxt-link :to="'/block/' + blockData.prev_header_hash_hex">
                {{blockData.prev_header_hash_hex}}  
              </nuxt-link>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
  <div class="card" v-if="TransactionBlock(blockData)"> 
    <v-expansion-panels>
      <v-expansion-panel>
        <v-expansion-panel-header>
          <h3>{{totalCoinsAdded}} Coins confirmed in this Block</h3>
        </v-expansion-panel-header>
      <v-expansion-panel-content>
    <div class="card flex">
      <v-pagination
        v-model="currentPageAdded"
        :length="totalPagesAdded"
        :total-visible="10">
      </v-pagination>
    </div>    
    <div class="card flex">
      <v-data-table  
        :headers="headers"
        :items="coinsAdded"
        :items-per-page="itemsPerPage"
        :currentPageAdded.sync="currentPageAdded"
        :server-items-length="totalCoinsAdded"
        :loading="loading"
        :pageCount = "currentPageAdded"
        class="elevation-1 text-left flex"
        hide-default-footer>

        <template v-slot:item.puzzle_hash="{item}">
          <nuxt-link :to="'/wallet/' + item.puzzle_hash">
            {{item.puzzle_hash}}
          </nuxt-link>
        </template>

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
          v-model="currentPageAdded"
          :length="totalPagesAdded"
          :total-visible="10">
        </v-pagination>
      </div>
      </v-expansion-panel-content>
    </v-expansion-panel>
  </v-expansion-panels>
  </div>
  <div class="card" v-if="TransactionBlock(blockData)"> 
    <v-expansion-panels>
      <v-expansion-panel>
        <v-expansion-panel-header>
          <h3>{{totalCoinsSpent}} Coins spent in this Block</h3>
        </v-expansion-panel-header>
      <v-expansion-panel-content>
    <div class="card flex">
      <v-pagination
        v-model="currentPageSpent"
        :length="totalPagesSpent"
        :total-visible="10">
      </v-pagination>
    </div>    
    <div class="card flex">
      <v-data-table  
        :headers="headers"
        :items="coinsSpent"
        :items-per-page="itemsPerPage"
        :currentPageSpent.sync="currentPageSpent"
        :server-items-length="totalCoinsSpent"
        :loading="loading"
        :pageCount = "currentPageSpent"
        class="elevation-1 text-left flex"
        hide-default-footer>

        <template v-slot:item.puzzle_hash="{item}">
          <nuxt-link :to="'/wallet/' + item.puzzle_hash">
            {{item.puzzle_hash}}
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
          v-model="currentPageAdded"
          :length="totalPagesAdded"
          :total-visible="10">
        </v-pagination>
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
      blockData: [],
      coinsAdded: [],
      itemsPerPage: 25,
      loading: true,
      totalCoinsAdded: 0,
      currentPageAdded: 1,
      totalPagesAdded: 0,
      coinsSpent: [],
      totalCoinsSpent: 0,
      currentPageSpent: 1,
      totalPagesSpent: 0,
      headers: [
        {text: "Type", value: "coinbase", sortable: false},
        {text: "Coin Name", value: "coin_name", sortable: false},
        {text: "Farmer Puzzle Hash", value: "puzzle_hash", sortable: false},
        {text: "Amount", value: "amount", sortable: false},
        {text: "Timestamp", value: "timestamp", sortable: false},
      ],
    };
  },
  async fetch() {
    const fetchData = await fetch("http://localhost:8080/stai/block/"+this.$route.params.block).then((res) => res.json());
    await this.loadData();
    this.blockData = fetchData.block
  },
  watch: {
  currentPageAdded() {
    }
  },
  methods: {
    async loadData() {
      const data = await fetch("http://localhost:8080/stai/block/"+this.$route.params.block).then((res) => res.json())
      this.coinsAdded = data.coinsAdded
      this.totalCoinsAdded = data.numOfResultsConfirmed
      this.totalPagesAdded = data.numberOfPages
      this.currentPageAdded = data.page

      this.coinsSpent = data.coinsSpent
      this.totalCoinsSpent = data.numOfResultsSpent
      this.totalPagesSpent = data.numberOfPagesSpent
      this.currentPageSpent = data.page

      this.loading = false
    },
    TransactionBlock(blockData) {
      if (blockData.timestamp > 0)
      return true 
    },
    formatTimestamp(blockData) {
      if (blockData.timestamp > 0)
      return new Date(blockData.timestamp*1000).toUTCString()
      else
      return "None"
    },
    formatType(blockData) {
      if (blockData.timestamp > 0)
      return "TX - Block"
      else
      return "Block"
    },
    formatCoinbase(item) {
      if (item.coinbase == true)
      return "Farmer Reward" 
      else
      return "Miscellaneous"
    }, 
    formatAmount(item) {
      return(item.amount/1000000000).toFixed(9)
    },
  }
  }
</script>

<style scoped>
a {
  text-decoration: none;
  color: #95BC0E;
}

.card {
    position: relative;
    border: 1px solid #95BC0E;
    background-color: #293131;
    background-clip: border-box;
    border: 1pxsolidrgba(0,0,0,.125);
    border-radius: 0.25rem;
}
.card-header {
    padding: 0.75rem 1.25rem;
    margin-bottom: 0;
    background-color: rgba(0,0,0,.03);
    border-bottom: 1px solid rgba(0,0,0,.125);
}
div {
    display: block;
}
th {
  background: #95BC0E;
  font-style: normal;
  color: #000000 !important;
  font-size:100% !important;
  }
td {
  background-color: #384141;
  color:#ffffff !important;
  display: block;
  width: 100;
}
.table {
  width: 100%;
  margin-bottom: 1rem;
  display: table;
  border-spacing: 2px;

  border-collapse: separate;
  box-sizing: border-box;
  text-indent: initial;
  border-color: grey;
}



.table.b-table.b-table-stacked>tbody>tr>[data-label]:before {
    content: attr(data-label);
    width: 40%;
    float: left;
    text-align: right;
    word-wrap: break-word;
    font-weight: 700;
    font-style: normal;
    padding: 0 1rem 0 0;
    margin: 0;
}
*, :after, :before {
  box-sizing: border-box;
}
.table-sm td, .table-sm th {
    padding: 0.3rem;
}
.table td, .table th {
    padding: 0.75rem;
    vertical-align: top;
    border-top: 1px solid #dee2e6;
    margin: 0.3rem;
}
tbody {
    display: table-row-group;
    vertical-align: middle;
    border-color: inherit;
}
.cards {
   display: flex;
   justify-content: space-between;
}
element.style {
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
}
.text-monospace {
    font-family: "Roboto Mono",monospace!important;
}
.mb-0, .my-0 {
    margin-bottom: 0!important;
}
.h1, .h2, .h3, .h4, .h5, .h6, .header-cards .card-title, h1, h2, h3, h4, h5, h6 {
    margin-bottom: 0.5rem;
    font-weight: 500;
    line-height: 1.2;
}
h1 {
    display: block;
    font-size: 2em;
    margin-block-start: 0.67em;
    margin-block-end: 0.67em;
    margin-inline-start: 0px;
    margin-inline-end: 0px;
    font-weight: bold;
}
.h1, h1 {
    font-size: 2.25rem;
}
h1, h2, h3, h4, h5, h6 {
    margin-top: 0;
    margin-bottom: 0.5rem;
}
</style>
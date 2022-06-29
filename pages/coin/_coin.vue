<template>
    <div>
  <div class="card">
    <div class="card-header flex">
      <h1 class="h3 text-monospace mb-0" style="overflow:hidden;white-space:nowrap;text-overflow:ellipsis;">
        <v-icon size="100%">mdi-cube-outline</v-icon> {{coinData.coin_name}}
      </h1>
    </div>
  </div>
  <div class=" flex">
    <table role="table" aria-busy="false" aria-colcount="11" class="table b-table table-sm b-table-stacked flex">
      <tbody role="rowgroup">
        <tr role="row" class>
          <td aria-colindex="1" data-label="Date:" role="cell" class>
            <div>
              {{formatTimestamp(coinData)}}
            </div>
          </td>
          <td aria-colindex="2" data-label="Type:" role="cell" class>
            <div>
                {{formatCoinbase(coinData)}}
            </div>
          </td>
          <td aria-colindex="3" data-label="Farmer Puzzle Hash:" role="cell" class>
            <div>
              {{coinData.puzzle_hash}}
            </div>
          </td>
          <td aria-colindex="4" data-label="Parent Coin:" role="cell" class>
            <div>
              {{coinData.coin_parent}}
            </div>
          </td>
          <td aria-colindex="5" data-label="amount:" role="cell" class>
            <div>
              {{formatAmount(coinData)}} STAI
            </div>
          </td>
          <td aria-colindex="6" data-label="Confirmed Index:" role="cell" class>
            <div>
              {{coinData.confirmed_index}}
            </div>
          </td>
          <td aria-colindex="7" data-label="Spent:" role="cell" class>
            <div>
              {{formatSpentIndex(coinData)}}
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</div>
</template>

<script>
export default {
  data() {
    return{
      coinData: [],
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
    const fetchData = await fetch("http://localhost:8080/stai/coin/"+this.$route.params.coin).then((res) => res.json());
    this.coinData = fetchData
  },
  methods: {
    formatTimestamp(fetchData) {
      if (fetchData.timestamp > 0)
      return new Date(fetchData.timestamp*1000).toUTCString()
      else
      return "None"
    },
    formatCoinbase(item) {
      if (item.coinbase == true)
      return "Farmer Reward" 
      else
      return "Miscellaneous"
    }, 
    formatSpentIndex(item) {
      if (item.spent_index > 0)
      return "Spent" 
      else
      return "Not Spent"
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
<template>
    <div class="container-fluid margins">
        <div class="row">
            <div class="col-10">
                <input type="text" class="form-control full-width" autofocus placeholder="Enter symbol..." v-model="symbol" v-on:keyup.enter="search"/>
            </div>
            <div class="col-2">
                <input type="button" class="btn btn-primary full-width" value="Go" v-on:click="search1" />
            </div>
        </div>
        <tabs-button @getdata="search" :selected="symbol" ></tabs-button>
        <stock-quote v-bind:stock="stock" v-bind:graph="graph"></stock-quote>
      
       <div class="alert alert-danger" role="alert" v-if="error">
         <strong>Error!</strong> {{this.error}}
       </div>
       <p class="small bottom">Data provided for free by
        <a href="https://iextrading.com/developer" target="_blank">IEX</a>.
        <br/> By using this application you agree to
        <a href="https://iextrading.com/api-exhibit-a" target="_blank">IEX terms of service</a>.
       </p>
    </div>
</template>
<script>
//include the quote component
var Quote = require("./quote.vue");
var Tabs = require("./tabsButtons.vue");
//export the needed properties
module.exports = {
  //the data model (props)
  data() {
    return {
      stock: {},
      graph: {},
      error: "",
      symbol:"",
      gItem:''
    };
  },
  //declare the included components, used in html
  components: {
    "stock-quote": Quote,
    "tabs-button": Tabs,
  },
  methods: {
    //init method, initialize props
    init() {
      this.stock = {};
      this.graph = {};
      this.error = "";
    },
    //search method, calls API
    getQuote() {
      let url = `https://api.iextrading.com/1.0/stock/${this.symbol}/quote`;
      // this.init();
      this.$http
        .get(url)
        .then(result=>{
          this.stock = result.data;
        })
        .catch(this.handleErrors);
    },
    search(item) {
        this.symbol = item;
        this.getGraphData()
        this.getQuote()
    },
    search1() {
        
        this.getGraphData()
        this.getQuote()
    },
    //gets chart data from the API
    getGraphData() {
      let gUrl = `http://localhost:3000/stock/?symbol=${this.symbol}`;
      this.$http
        .get(gUrl)
        .then(result => {
          this.graph = result.data.data[0].attributes;
        })
        .catch(this.handleErrors);
    },
    //method to handle errors
    handleErrors(err) {
      if (err.status === 404) {
        this.error = "The specified symbol could not be found...";
      } else {
        this.error = "There was an error retrieving the data...";
      }
    },
    getDefault(){
      let gUrl = `http://localhost:3000/stock/default`;
      this.$http
        .get(gUrl)
        .then(result => {
          this.graph = result.data.data[0].attributes;
          this.symbol = this.graph[0].symbol
          this.getQuote()
        })
        .catch(this.handleErrors);
    }
  },
  mounted(){
    this.getDefault()
  }
};
</script>
<style>
.margins {
  padding: 40px 30px;
}
.full-width {
  width: 100%;
}
.small {
  font-size: 8px;
  color: #c0c0c0;
}
.bottom {
  position: absolute;
  bottom: 5px;
  margin: 0 auto;
}
</style>
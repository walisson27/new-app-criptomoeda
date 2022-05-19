<template>

  <div class="container mt-4 mt-md-5">
      <div class="home-container shadow">
        <h1 class="mb-5">Cotação das Criptomoedas</h1>
      <div class="row">
        <div class="col-12 col-md-6">
          <b-form-group
            label="Moeda:"
            >
           <b-form-select v-model="currencieSelect" :options="currencieOptions" class="form-control" size="sm"></b-form-select>
          </b-form-group>
        </div>

        <div class="col-12 col-md-6">
          <b-form-group
            label="Criptomoeda:"
            >
            <b-form-select v-model="coinSelect" :options="coinOptions" class="form-control" size="sm"></b-form-select>
          </b-form-group>
        </div>
      </div>
   
      <card-crypto
        :loading="loading"
        :rank="data.market_cap_rank"
        :image="data.image"
        :coinName="data.name"
        :symbol="data.symbol"
        :currency="currencieSelect"
        :currentPrice="moedaFormatada"
        :echangePorcentage="data.price_change_percentage_24h"
      /> 

       <div class="row">    
         <div class="col-12 col-md-5">
            <b-form-group
            label="Data:"
            >
            <b-form-input type="date" v-model="dateValue" required></b-form-input>
          </b-form-group>
         </div>

          <div class="col-12 col-md-5 mt-2 mt-md-0">
            <b-form-group
              label="Hora:"
              >
              <b-form-input type="time" v-model="timeValue" required></b-form-input>
            </b-form-group>
          </div>

          <div class="col-12 col-md-2 align-self-end">
            <button @click="priceFilter" class="btn btn-primary mt-2" style="width: 100%;">Buscar</button>
          </div>
      </div>
      <div class="row mt-4">
        <div class="col">
          
          <b-form-group
              label="Total:"
              >
              <input type="text" class="form-control" :value="priceFiltered" disabled>

          </b-form-group>
        </div>
      </div>
      </div>
  </div>

</template>

<script>
import api from "@/services.js";
import CardCrypto from "@/components/CardCrypto.vue";

export default {
  data() {
    return {
      polling: null,
      errFilter: false,
      timeValue: "",
      dateValue: "",
      data: [],
      loading: false,
      baseURL: "https://api.coingecko.com/api/v3/coins/markets",
      coinSelect: "bitcoin",
      currencieSelect: "usd",
      currencieOptions: [
          { value: "usd", text: 'USD' },
          { value: 'brl', text: 'BRL' }
        ],
        coinOptions: [
          { value: "bitcoin", text: 'Bitcoin' },
          { value: 'ethereum', text: 'Ethereum' },
          { value: "dacxi", text: "Dacxi" },
          { value: "terra-luna", text: "Terra" },

        ],
      priceFiltered: null
    };
  },
  computed: {
    moedaFormatada(){
      if(this.data.current_price){
        return this.priceFormatter(this.data.current_price, this.currencieSelect)
      }
    }
  },
  watch: {
    currencieSelect(){
      if(this.priceFiltered){
        this.priceFilter()
        this.getCoin()
      } else {
        this.getCoin()
      }
    },
    coinSelect(){
      if(this.priceFiltered){
        this.priceFilter()
        this.getCoin()
      } else {
        this.getCoin()
      }
    },
  },
  methods: {
      pollData () {
      this.polling = setInterval(() => {
        this.getCoin()
      }, 15000)
    },
    clearFilter(){
      this.priceFiltered = null
    },
    async getCoin() {
      this.loading = true;
      const url = `/coins/markets?vs_currency=${this.currencieSelect}&ids=${this.coinSelect}`;
      const coin = await api.get(url);
      this.data = await coin.data[0];
      this.loading = false;
    },
    convertDate(datevalue, timevalue){
      const dateStr = `${datevalue} ${timevalue}`
      let seconds = "00"
      const [dateComponents, timeComponents] = dateStr.split(' ');
      const [year, month, day] = dateComponents.split('-');
      const [hours, minutes] = timeComponents.split(':');
      const date = new Date(+year, month - 1, +day, +hours, +minutes, +seconds)
      const unixTimestamp = Math.floor(date.getTime() / 1000);
      return unixTimestamp;
    },
    async priceFilter(){

      const daySeconds = 86400;
      const to = await this.convertDate(this.dateValue, this.timeValue);
      const from = await to - daySeconds;
      const priceFiltered = await api.get(`/coins/${this.coinSelect}/market_chart/range?vs_currency=${this.currencieSelect}&from=${from}&to=${to}`)
      if ( priceFiltered.data.prices.length > 0) {
        this.priceFiltered = this.priceFormatter(priceFiltered.data.prices[priceFiltered.data.prices.length - 1][1], this.currencieSelect)
      } else {
        this.priceFiltered = "Data incorreta"
      }
    },
    priceFormatter(e, code) {
      if (code === "usd") {
        return "US" + e.toLocaleString( "en-US", {style: "currency", currency: code, minimumFractionDigits: 4 } )
      } else {
        return e.toLocaleString( "pt-BR", {style: "currency", currency: code, minimumFractionDigits: 4} )
      }
    },
  },
  components: {
    CardCrypto
  },
    beforeDestroy () {
    clearInterval(this.polling)
  },
  created() {
    this.getCoin();
    this.pollData();
  },
};

</script>

<style>
.home-container {
  max-width: 600px;
  margin: 0 auto;
  margin-bottom: 80px;
  padding: 20px;
  border-radius: 20px;
}

.home-container h1 {
  font-size: 28px;
  font-weight: bold;
}


div#app{
 background-image: linear-gradient( 110.3deg,  rgba(72,85,99,1) 8.8%, rgba(127,146,166,1) 95.1% );
}

html{
  background-image: linear-gradient( 110.3deg,  rgba(72,85,99,1) 8.8%, rgba(127,146,166,1) 95.1% );
}
</style>
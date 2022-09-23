<template>
  <div class="flex items-center justify-center h-5/6">
    <div class="rounded overflow-hidden shadow-lg mr-4">

      <div class="px-6 py-4">
        <div class="font-bold text-xl text-center">Conversiones</div>
      </div>

      <div>
        <div class="place-content-center my-2">
          <div class="my-2 sm:-mx-6 lg:-mx-8">
            <div
              class="py-2 align-middle inline-block min-w-full sm:px-6 lg:px-8">
              <div class="shadow border-b border-gray-200 sm:rounded-lg">
                <button @click="changeConvert()" class="bg-green-500 hover:bg-blue-600 text-white font-bold py-1 px-4 rounded mx-2 mr-6">
                    {{ this.convertBTCtoUSD == true ? 'USD a BTC' : 'BTC a USD' }}
                </button>
                <table class="min-w-full divide-y divide-gray-200">
                  <thead v-if="this.convertBTCtoUSD == true" class="bg-gray-50">
                    <tr>
                      <th scope="col" class="py-3 text-center items-center text-xs font-medium text-gray-500 uppercase tracking-wider">
                        <img src="@/assets/bitcoin.png" class="w-10">
                      </th>
                      <th scope="col" class=" py-3 text-center items-center text-xs font-medium text-gray-500 uppercase tracking-wider">
                        <input v-model="this.btc" @change="changedValueBTC()" type="text" class="block w-full py-2 px-3 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm w-48 mr-8" />
                      </th>
                      <th scope="col" class="py-3 text-center items-center text-xs font-medium text-gray-500 uppercase tracking-wider">
                        <img src="@/assets/igual.png" class="w-10">
                      </th>
                      <th scope="col" class="pl-6 py-3 text-center items-center text-xs font-medium text-gray-500 uppercase tracking-wider">
                        <img src="@/assets/dollar.png" class="w-10">
                      </th>
                      <th scope="col" class="p py-3 text-center items-center text-xs font-medium text-gray-500 uppercase tracking-wider">
                        <input v-if="this.btc == 1" v-model="this.priceActualBTC" type="text" class="block w-full py-2 px-3 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm w-48 mr-8" />
                        <input v-if="this.btc != 1" v-model="this.btc_x_number" type="text" class="block w-full py-2 px-3 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm w-48 mr-8" />
                      </th>
                    </tr>
                  </thead>
                  <thead v-if="this.convertBTCtoUSD == false" class="bg-gray-50">
                    <tr>
                      <th scope="col" class="py-3 text-center items-center text-xs font-medium text-gray-500 uppercase tracking-wider">
                        <img src="@/assets/dollar.png" class="w-10">
                      </th>
                      <th scope="col" class="p py-3 text-center items-center text-xs font-medium text-gray-500 uppercase tracking-wider">
                        <input v-model="this.dolar" @change="changevalueDolar()" type="number" class="block w-full py-2 px-3 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm w-48 mr-8" />
                      </th>
                      <th scope="col" class="py-3 text-center items-center text-xs font-medium text-gray-500 uppercase tracking-wider">
                        <img src="@/assets/igual.png" class="w-10">
                      </th>
                      <th scope="col" class="pl-6 py-3 text-center items-center text-xs font-medium text-gray-500 uppercase tracking-wider">
                        <img src="@/assets/bitcoin.png" class="w-10">
                      </th>
                      <th scope="col" class=" py-3 text-center items-center text-xs font-medium text-gray-500 uppercase tracking-wider">
                        <input v-model="this.btc2"  type="number" class="block w-full py-2 px-3 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm w-48 mr-8" />
                      </th>
                    </tr>
                  </thead>             
                </table>
              </div>
            </div>
          </div>
        </div>
      </div>

    </div>
  </div>
</template>
<script>
import axios from "axios";

import Swal from 'sweetalert2'


export default {
  name: "AppConversion",
  data() {
    return {
      convertBTCtoUSD: false,
      dataPrice: null,
      priceActualBTC: 0.0,
      btc_x_number: 0.0,
      dolar: 0.0,
      btc: 1,
      btc2: 0.0,
    };
  },
  methods: {
    backData: function () {
      axios
        .get("http://localhost:7000/precio-btc")
        .then((resp) => {
            this.dataPrice = resp.data.data
            this.priceActualBTC = this.dataPrice.amount
           
        })
        .catch((error) => {
          console.log(error);
          this.cleanInput()
          Swal.fire({
                title: 'Mensaje',
                icon: 'info',
                text: 'No se encontro el pais...',
            });
          
        });
    },
    cleanInput: function(){
      this.dataPrice = null
    },
    changedValueBTC: function(){
      this.btc_x_number = this.btc * this.priceActualBTC
    },
    changevalueDolar: function(){
      /* 
        FORMULA
        19419.51 USD   =>    1 BTC
        °N  USD        =>    X BTC

        (°N * 1)/ 19419.51 USD
      */
      var result = (this.dolar * 1)/this.priceActualBTC
       this.btc2 = result.toFixed(8)
      
    },
    changeConvert: function(){
      if (this.convertBTCtoUSD == true) {
        this.convertBTCtoUSD = false

      } else {
        if (this.convertBTCtoUSD == false) {
          this.convertBTCtoUSD = true

        }
      }
    }

  },
  mounted(){
    this.backData()
    setInterval(()=> this.backData(),15000)
    
  }
};
</script>

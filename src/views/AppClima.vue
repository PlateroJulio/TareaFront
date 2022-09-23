<template>
  <div class="flex items-center justify-center h-5/6">
    <div class="rounded overflow-hidden shadow-lg mr-4">

      <div class="px-6 py-4">
        <div class="font-bold text-xl text-center">Clima</div>
      </div>

      <div>
        <div class="place-content-center my-2">
          <div class="my-2 sm:-mx-6 lg:-mx-8">
            <div
              class="py-2 align-middle inline-block min-w-full sm:px-6 lg:px-8">
              <div class="shadow border-b border-gray-200 sm:rounded-lg">
                <table class="min-w-full divide-y divide-gray-200">
                  <thead class="bg-gray-50">
                    <tr>
                      <th
                        scope="col"
                        class="px-6 py-3 text-center items-center text-xs font-medium text-gray-500 uppercase tracking-wider"
                      >
                        NOMBRE DE PAIS:
                      </th>
                      <th
                        scope="col"
                        class="px-6 py-3 text-center items-center text-xs font-medium text-gray-500 uppercase tracking-wider"
                      >
                        <input
                          v-model="pais"
                          type="text"
                          class="block w-full py-2 px-3 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm w-48 mr-8"
                        />
                      </th>
                      <th
                        scope="col"
                        class="px-6 py-3 text-center items-center text-xs font-medium text-gray-500 uppercase tracking-wider"
                      >
                        <button
                          @click="backData()"
                          class="bg-green-500 hover:bg-blue-600 text-white font-bold py-1 px-4 rounded mx-2 mr-6"
                        >
                          Buscar
                        </button>
                      </th>
                    </tr>
                  </thead>
                  <tbody v-if="this.dataClima != null" :class=" this.dayOrNight == 'd'? 'bg-yellow-400 divide-y divide-gray-200' : 'bg-blue-900 divide-y divide-gray-200'">
                    <tr :class=" this.dayOrNight == 'd' ? 'text-black font-bold' : 'text-white font-bold' ">
                      <td class="text-center px-6 py-4 whitespace-nowrap">
                        <div class="ml-4">
                          <div class="text-md ">
                            {{ this.main_ }}
                          </div>
                        </div>
                      </td>
                      <td>
                        <div class="ml-4">
                          <div class="text-md">
                            {{ this.description }}
                          </div>
                        </div>
                      </td>
                      <td>
                        <div class="ml-4">
                          <div class="text-sm">
                            <img
                              :src='this.icon == "" ? require("@/assets/openweatermap/01d.png") : require("@/assets/openweatermap/"+this.icon)'
                            />
                          </div>
                        </div>
                      </td>
                    </tr>

                    <tr :class=" this.dayOrNight == 'd' ? 'text-black font-bold' : 'text-white font-bold' ">
                      <td class="text-center px-6 py-4 whitespace-nowrap">
                        <div class="ml-4">
                          <div class="text-sm">
                            Temp: {{ this.temp }} °C
                          </div>
                        </div>
                      </td>
                      <td>
                        <div class="ml-4">
                          <div class="text-sm t">
                            Temp min: {{ this.temp_min }} °C
                          </div>
                        </div>
                      </td>
                      <td>
                        <div class="ml-4">
                          <div class="text-sm ">
                            Temp max: {{ this.temp_max }} °C
                          </div>
                        </div>
                      </td>
                    </tr>

                    <tr :class=" this.dayOrNight == 'd' ? 'text-black font-bold' : 'text-white font-bold' ">
                      <td class="text-center px-6 py-4 whitespace-nowrap">
                        <div class="ml-4">
                          <div class="text-sm">
                            Feels like: {{ this.feels_like }} °C
                          </div>
                        </div>
                      </td>
                      <td>
                        <div class="ml-4">
                          <div class="text-sm ">
                           Humidity: {{ this.humidity }} °C
                          </div>
                        </div>
                      </td>
                      <td>
                        <div class="ml-4">
                          <div class="text-sm">

                          </div>
                        </div>
                      </td>
                    </tr>
                  </tbody>
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
  name: "AppClima",
  data() {
    return {
      pais: "",
      dataClima: null,
      //weater
      main_: "",
      description: "",
      icon: "",
      //main
      temp: "",
      feels_like: "",
      temp_min: "",
      temp_max: "",
      humidity: "",
      dayOrNight: ""
    };
  },
  methods: {
    backData: function () {
      axios
        .get("http://localhost:7000/consulta-clima/" + this.pais)
        .then((resp) => {
          this.dataClima = resp.data.Mensaje;

          this.main_ = this.dataClima.weather[0].main;
          this.description = this.dataClima.weather[0].description;
          this.icon = this.dataClima.weather[0].icon + ".png";

          this.temp = this.dataClima.main.temp;
          this.feels_like = this.dataClima.main.feels_like;
          this.temp_min = this.dataClima.main.temp_min;
          this.temp_max = this.dataClima.main.temp_max;
          this.humidity = this.dataClima.main.humidity;

          var question = this.dataClima.weather[0].icon
          var index = question.length
          var sub = question.substring(index-1, index)
          this.dayOrNight = sub

           
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
      this.dataClima = null

      this.main_ = ""
      this.description = ""
      this.icon = ""

      this.temp = ""
      this.feels_like = ""
      this.temp_min = ""
      this.temp_max = ""
      this.humidity = ""

      this.dayOrNight = ""
    }
  },
};
</script>

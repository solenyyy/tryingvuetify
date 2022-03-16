/* eslint-disable vue/no-parsing-error */
<template>
  <v-app>
    <v-container>
      <v-row class="text-center">
        <v-col class="mb-4">
          <div class="circle"></div>
          <h1>
            <span>Escribe</span>
            <br />
            <span>tu</span>
            <br />
            <span>nombre</span>
            <br />
            <span>y te diré</span>
            <br />
            <span>cosas sobre él</span>
          </h1>
          <v-text-field ref="form" v-model="nombre" label="Tu Nombre" required></v-text-field>
  <v-alert  v-if="errors.length"
  color="red"
  type="warning"
  style="width: 300px; margin: -15px auto 15px auto;">
  <p>
    <b>Ha ocurrido un problema:</b>
    <ul>
      <li v-for="error in errors" v-bind:key="error">{{ error }}</li>
    </ul>
  </p>
  </v-alert>
          <div class="check-container" id="selector">
            <input type="checkbox" id="checkbox" v-model="checked" />
            <label for="checkbox">Selecciona si te identificas como género no binario.</label>
          </div>
          <br />
          <v-btn
            color="#6B5CA5"
            class="mr-4"
            theme="dark"
            @click="loading = true; sended = true; getAllData()"
          >Enviar</v-btn>
                    <v-btn
            color="#72195a"
            class="mr-4"
            theme="dark"
            @click="reloadPage()"
          >Refresh</v-btn>
        </v-col>
      </v-row>
    </v-container>
    <v-progress-linear
    v-if="!errors.length"
      :active="loading"
      :indeterminate="loading"
      absolute
      bottom
      color="#4C1036"
      style="width: 300px;"
    ></v-progress-linear>
    <v-scale-transition v-if="sended === true">
      <div class="container-margin" v-if="!loading">
        <v-card max-width="450" class="mx-auto" v-if="!errors.length">
          <v-container>
            <v-row dense>
              <v-col cols="12">
                <v-card color="#71A9F7" theme="dark">
                  <v-card-title
                    class="text-h6"
                  >La edad promedio de las personas que se llaman como tú:</v-card-title>

                  <v-card-subtitle v-if="age != null">{{ age }} años</v-card-subtitle>
                </v-card>
              </v-col>

              <v-col cols="12" v-if="!checked">
                <v-card color="#6B5CA5" theme="dark">
                  <div class="d-flex flex-no-wrap justify-space-between">
                    <div>
                      <v-card-title
                        class="text-h6"
                      >Las personas con tu nombre se identifican con el género:</v-card-title>

                      <v-card-subtitle
                        v-if="this.gender.length > 1"
                      >{{ gender }} {{ estadistica.probability }}</v-card-subtitle>
                    </div>
                  </div>
                </v-card>
              </v-col>

              <v-col cols="12">
                <v-card color="#72195A" theme="dark">
                  <div class="d-flex flex-no-wrap justify-space-between">
                    <div>
                      <v-card-title class="text-h6">Los países donde más existe tu nombre:</v-card-title>

                      <v-card-subtitle
                        v-for="country in estadistica.countries"
                        :key="country"
                      >{{ country }}</v-card-subtitle>
                    </div>
                  </div>
                </v-card>
              </v-col>
            </v-row>
          </v-container>
        </v-card>
      </div>
    </v-scale-transition>
  </v-app>
</template>

<script>
const axios = require("axios");// require = fetch/axios
const emojiFlags = require('emoji-flags');
export default {
  name: 'HelloWorld',
  el: '#selector',

  data: () => ({
    nombre: '',
    sended: false,
    checked: false,
    loading: false,
    age: null,
    gender: {},
    estadistica: {},
    errors: [],
    ecosystem: [
      {
        text: 'vuetify-loader',
        href: 'https://github.com/vuetifyjs/vuetify-loader',
      },
      {
        text: 'github',
        href: 'https://github.com/vuetifyjs/vuetify',
      },
      {
        text: 'awesome-vuetify',
        href: 'https://github.com/vuetifyjs/awesome-vuetify',
      },
    ],
  }),
  methods: {
    async getAllData() {
      if (!this.nombre)
        this.errors = [],
          this.errors.push("El nombre es obligatorio.")
      else if (this.nombre.length <= 3)
        this.errors = [],
          this.errors.push("El nombre tiene que ser mayor a 3 caracteres.")
      else {
        this.errors = []
        const tasks = [this.getName(), this.getGender(), this.getNation()];
        await Promise.all(tasks)
        this.loading = false
      }
    },
    async getName() {
      const { data } = await axios.get(`https://api.agify.io?name=${this.nombre}`);
      this.age = data.age;
      this.estadistica.age = data.age
    },
    async getGender() {
      const { data } = await axios.get(`https://api.genderize.io?name=${this.nombre}`);
      this.gender = data.gender === "female" ? "Femenino" : "Masculino"
      this.estadistica.gender = data.gender
      this.estadistica.probability = data.probability
    },
    async getNation() {
      const { data } = await axios.get(`https://api.nationalize.io?name=${this.nombre}`);
      let countries = data.country.map(country => country.country_id)
      let countriesWithEmojis = countries.map(this.emojifyCountry)
      this.estadistica.countries = countriesWithEmojis.slice(0, 2);
    },
    emojifyCountry(countryCode) {
      const emoji = emojiFlags.countryCode(countryCode);
      return `${emoji.name}`
    },
    reloadPage() {
      window.location.reload()
    }
  },
  watch: {
    loading(val) {
      if (!val) return
    },
  },
}
</script>
<style scoped>
@import url("https://fonts.googleapis.com/css?family=Lobster");

.container-margin {
  margin-bottom: 2rem;
}
.v-application {
  background-color: #f3f2f8 !important;
}
.v-field {
  margin: 0 auto;
  max-width: 50%;
  margin-top: 2.8rem;
}
.v-btn.v-btn--density-default {
  color: white;
}
.v-progress-linear {
  margin: 0 auto;
}
.v-card--variant-contained {
  box-shadow: 0px 1px 10px 0px rgb(0 0 0 / 20%),
    0px 1px 10px 0px rgb(0 0 0 / 14%), 0px 1px 3px 0px rgb(0 0 0 / 12%);
}
.v-card-subtitle {
  font-size: 1.11rem;
  opacity: 0.85;
  padding-bottom: 10px;
}

/* checkbox */

input[type="checkbox"] {
  display: none;
}

input[type="checkbox"] + label {
  cursor: pointer;
} /*esta linea significa esto + el siguiente elemento*/

label:before {
  content: "";
  background: transparent;
  border: 3px solid #71a9f7;
  border-radius: 25px;
  display: inline-block;
  height: 25px;
  width: 25px;
  margin-right: 20px;
  text-align: center;
  vertical-align: middle;
}
input[type="checkbox"]:checked + label:before {
  content: "✔";
  font-size: 15px;
  font-family: "Times New Roman";
  color: #72195a;
}
/*  title */
.circle {
  margin: auto;
  height: 270px;
  width: 270px;
  border-radius: 100%;
  background: #71a9f7;
  margin-top: 40px;
}

h1 {
  font-family: "Lobster", cursive;
  color: #cfcae2;
  text-align: center;
  line-height: 50px;
  margin-top: -300px;
  -moz-transform: scale(1) rotate(-5deg) translateX(0px) translateY(0px)
    skewX(1deg) skewY(1deg);
  -webkit-transform: scale(1) rotate(-5deg) translateX(0px) translateY(0px)
    skewX(1deg) skewY(1deg);
  -o-transform: scale(1) rotate(-5deg) translateX(0px) translateY(0px)
    skewX(1deg) skewY(1deg);
  -ms-transform: scale(1) rotate(-5deg) translateX(0px) translateY(0px)
    skewX(1deg) skewY(1deg);
  transform: scale(1) rotate(-5deg) translateX(0px) translateY(0px) skewX(1deg)
    skewY(1deg);
  /*-----------------------*/
  text-shadow: 1px 1px #72195a, 2px 2px #72195a, 3px 3px #72195a,
    4px 4px #72195a, 5px 5px #72195a, 6px 6px #72195a, 7px 7px #72195a,
    8px 8px #72195a, 9px 9px #72195a, 10px 10px #72195a, 11px 11px #72195a,
    12px 12px #72195a, 13px 13px #72195a, 14px 14px #72195a, 15px 15px #72195a,
    16px 16px #72195a, 17px 17px #72195a, 18px 18px #72195a, 19px 19px #72195a,
    20px 20px #72195a, 21px 21px #72195a, 22px 22px #72195a, 23px 23px #72195a,
    26px 26px 6px #cfcae2;
}

span:nth-child(3),
span:nth-child(7) {
  font-size: 30px;
}

span:nth-child(1),
span:nth-child(9),
span:nth-child(5) {
  font-size: 45px;
}
</style>

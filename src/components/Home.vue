<template>
  <v-container>
    <v-row class="text-center" align="center" justify="center">
      <v-col cols="12">
        <Logo />
        <h1  class="zenkoku">コロナ アプリ</h1>

      </v-col>
      <v-spacer />
    </v-row>

    <v-row class="text-center" align="center" justify="center">
      <v-col cols="12">
        <v-spacer />
      </v-col>
      <v-col cols="12">
        <h1>全国</h1>
      </v-col>
      <!-- Country value -->
      <v-col cols="12">
        <section class="country col-md-3 text-center mx-auto">
          <model-select
            :options="options"
            @input="changeCountry"
            v-model="selectedCountry"
            placeholder="Select Country"
          ></model-select>
        </section>
      </v-col>

      <v-alert type="error" v-if="countryError">Oops! The country's information could not be found.</v-alert>
    
      <v-col cols="12">
        <p class="text">新更新: {{ countriesUpdate.lastUpdate }}</p>
      </v-col>


      <v-col cols="12" md="3">
        <div class="mx-auto pt-3 pb-3" shaped :loading="loading">
          <v-card-text class="card">
            <p class="display-2 orange--text">{{ countriesUpdate.confirmed | putComma }}</p>
            <small class="warning--text">+{{ todayInCountries }} 前日比</small>
            <h2>累計感染者数</h2>
          </v-card-text>
        </div>
      </v-col>

      <v-col cols="12" md="3">
        <div class="mx-auto pt-3 pb-3" shaped :loading="loading">
          <v-card-text class="card">
            <p
              class="display-2"
              style="color:rgb(236, 49, 75)"
            >{{ countriesUpdate.deaths | putComma}}</p>
            <small class="error--text">{{ countryDeathPercentage }}%</small>

            <h2>死亡者数</h2>
          </v-card-text>
        </div>
      </v-col>

      <v-col cols="12" md="3">
        <div class="mx-auto pt-3 pb-3" shaped :loading="loading">
          <v-card-text class="card">
            <p
              class="display-2"
              style="color:rgb(5, 181, 132)"
            >{{ countriesUpdate.recovered | putComma }}</p>
            <small class="success--text">{{ countryRecoveredPercentage }}%</small>

            <h2>退院者数</h2>
          </v-card-text>
        </div>
      </v-col>
    </v-row>



    <v-row class="text-center" align="center" justify="center">
      <v-col cols="12">
        <h1 class="sekai" style="color:rgb(33, 43, 54)">世界</h1>
        <p class="text">新更新: {{ global.lastUpdate }}</p>
      </v-col>


      <v-col cols="12" md="3">
        <div class="mx-auto pt-3 pb-3" shaped :loading="globalLoading">
          <v-card-text class="card">
            <p class="display-1 orange--text">{{ global.confirmed | putComma }}</p>
            <small class="warning--text">+{{ todayInGlobal }} 前日比</small>

            <h2>累計感染者数</h2>
          </v-card-text>
        </div>
      </v-col>

      <v-col cols="12" md="3">
        <div class="mx-auto pt-3 pb-3" shaped :loading="globalLoading">
          <v-card-text class="card">
            <p class="display-1" style="color:rgb(236, 49, 75)">{{ global.deaths | putComma }}</p>
            <small class="error--text">{{ globalDeathPercentage }}%</small>
            <h2>退院者数</h2>
          </v-card-text>
        </div>
      </v-col>

      
      <v-col cols="12" md="3">
        <div class="mx-auto pt-3 pb-3" shaped :loading="globalLoading">
          <v-card-text class="card">
            <p class="display-1" style="color:rgb(5, 181, 132)">{{ global.recovered | putComma }}</p>
            <small class="success--text">{{ globalRecoveredPercentage }}%</small>

            <h2>退院者数</h2>
          </v-card-text>
        </div>
      </v-col>
    </v-row>
  </v-container>
</template>

  <script>
import Logo from "./SvgLogo";
import axios from "axios";
import moment from "moment";
import countries from "./countries.json";
import { ModelSelect } from "vue-search-select";
export default {
  name: "Home",
  components: { Logo, ModelSelect },
  data: () => ({
    data: "",
    todayInCountries: 0,
    countryDeathPercentage: 0,
    countryRecoveredPercentage: 0,
    todayInGlobal: 0,
    globalDeathPercentage: 0,
    globalRecoveredPercentage: 0,
    countryError: false,
    loading: false,
    globalLoading: false,
    options: [],
    api_key: "69147e9b0fa48fea1bcd1a45d2aa0664168d849ba7a6c5d6150c4f81", // 無料アカウント
    selectedCountry: {
      value: "",
      text: ""
    },
    baseEndpoint: "https://covid19.mathdro.id/api",
    global: {
      confirmed: 0,
      deaths: 0,
      recovered: 0,
      lastUpdate: ""
    },
    countriesUpdate: {
      confirmed: 0,
      deaths: 0,
      recovered: 0,
      lastUpdate: ""
    }
  }),
  methods: {
    async locatedCountry() {
      let { data } = await axios.get(
        `https://api.ipdata.co/?api-key=${this.api_key}`
      );
      this.selectedCountry.value = data.country_code;
      this.selectedCountry.text = data.country_name;
      this.changeCountry();
    },
    async getRootData() {
      this.globalLoading = true;
      const response = await axios.get(this.baseEndpoint);
      this.data = response.data;
      // console.log(this.data);データを確認
      this.global.confirmed = this.data.confirmed.value;
      this.global.deaths = this.data.deaths.value;
      this.global.recovered = this.data.recovered.value;
      this.global.lastUpdate = moment(this.data.lastUpdate).format(
        "MMMM Do YYYY, h:mm:ss a"
      );
      this.globalLoading = false;
    },
    async getCountries() {
      for (let [text, value] of Object.entries(countries)) {
        this.options.push({ text, value });
      }
    },
    async changeCountry() {
      this.countryRecoveredPercentage = 0;
      this.countryDeathPercentage = 0;
      this.todayInCountries = 0;
      for (let [key] of Object.entries(this.countriesUpdate)) {
        this.countriesUpdate[key] = 0;
      }
      this.countryError = false;
      this.loading = true;
      const { data } = await axios
        .get(`${this.baseEndpoint}/countries/${this.selectedCountry.value}`)
        .catch(() => {
          this.loading = false;
          this.countryError = true;
          setTimeout(() => {
            this.countryError = false;
          }, 5000);
          this.countriesUpdate.confirmed = 0;
          this.countriesUpdate.deaths = 0;
          this.countriesUpdate.recovered = 0;
          this.countriesUpdate.lastUpdate = "";
        });
      const yesterday = moment()
        .subtract(1, "days")
        .startOf("day")
        .format("M-D-YYYY");
      const beforeYesterday = moment()
        .subtract(2, "days")
        .startOf("day")
        .format("M-D-YYYY");
      let yesterdayResponse;
      let beforeYesterdayResponse;
      try {
        yesterdayResponse = await axios.get(
          `${this.baseEndpoint}/daily/${yesterday}`
        );
        beforeYesterdayResponse = await axios.get(
          `${this.baseEndpoint}/daily/${beforeYesterday}`
        );
      } catch (error) {
        const tempYesterday = moment()
          .subtract(2, "days")
          .startOf("day")
          .format("M-D-YYYY");
        yesterdayResponse = await axios.get(
          `${this.baseEndpoint}/daily/${tempYesterday}`
        );
        const tempBeforeYesterday = moment()
          .subtract(3, "days")
          .startOf("day")
          .format("M-D-YYYY");
        beforeYesterdayResponse = await axios.get(
          `${this.baseEndpoint}/daily/${tempBeforeYesterday}`
        );
      }
      let yesterdayConfirm = 0;
      let beforeYesterdayConfirm = 0;
      let globalYesterdayConfirm = 0;
      let globalBeforeYesterdayConfirm = 0;
      yesterdayResponse.data.map(data => {
        globalYesterdayConfirm += Number(data.confirmed);
        if (data.countryRegion == this.selectedCountry.text) {
          yesterdayConfirm += Number(data.confirmed);
        }
      });
      beforeYesterdayResponse.data.map(data => {
        globalBeforeYesterdayConfirm += Number(data.confirmed);
        if (data.countryRegion == this.selectedCountry.text) {
          beforeYesterdayConfirm += Number(data.confirmed);
        }
      });
      this.loading = false;
      this.countriesUpdate.confirmed = data.confirmed.value;
      this.countriesUpdate.deaths = data.deaths.value;
      this.countriesUpdate.recovered = data.recovered.value;
      this.countriesUpdate.lastUpdate = moment(data.lastUpdate).format(
        "MMMM Do YYYY, h:mm:ss a"
      );
      // 前日比の世界の感染者数
      this.todayInGlobal =
        globalYesterdayConfirm -
        globalBeforeYesterdayConfirm +
        (this.global.confirmed - globalYesterdayConfirm);
      // 前日比の各国の感染者数
      this.todayInCountries =
        yesterdayConfirm -
        beforeYesterdayConfirm +
        (this.countriesUpdate.confirmed - yesterdayConfirm);
      // 世界の退院者数をパーセント化
      this.globalRecoveredPercentage = Math.round(
        (this.global.recovered / this.global.confirmed) * 100
      );
      // 各国の退院者数をパーセント化
      this.countryRecoveredPercentage = Math.round(
        (this.countriesUpdate.recovered / this.countriesUpdate.confirmed) * 100
      );
      // 世界の死亡者数をパーセント化
      this.globalDeathPercentage = Math.round(
        (this.global.deaths / this.global.confirmed) * 100
      );
      // 各国の死亡者数をパーセント化
      this.countryDeathPercentage = Math.round(
        (this.countriesUpdate.deaths / this.countriesUpdate.confirmed) * 100
      );
    }
  },
  filters: {
    putComma(number) {
      return Number(number).toLocaleString();
    }
  },
  created() {
    this.getRootData();
    this.getCountries();
    this.locatedCountry();
  }
};
</script>
  <style  scoped>

h1,
h2,
h3,
h4,
h5,
h6 {
  font-weight:600;
  color: rgb(83, 83, 83);
}
.text{
    color: rgb(76, 175, 80);
}
.sekai{
   margin: 60px auto 20px auto;
}
small {
  font-size: 14px;
}
.zenkoku{
    margin: 20px auto 0 auto;
    font-size: 40px;
    font-weight: 900;
}
.card{
     padding: 25px;
border-radius: 50px;
background: #ffffff;
box-shadow:  20px 20px 60px #d9d9d9,
             -20px -20px 60px #ffffff;
}
.country{
   
    margin:20px auto;
border-radius: 10px;
background: #ffffff;
box-shadow:  20px 20px 60px #d9d9d9,
             -20px -20px 60px #ffffff;
}
.ui.selection.dropdown{
    border: rgba(172, 242, 255, 0.657) 2px solid;
    font-size: 20px;
}
.container{
    width: 95%;
}

@media (min-width: 960px){
    .container{
        max-width: 1024px;
    }
}
</style>
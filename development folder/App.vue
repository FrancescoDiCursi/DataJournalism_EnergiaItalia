<script>
import Vue from "vue";
import FirstPage from "../src/components/FirstPage.vue";
import Home from "./components/Home.vue";
import { BootstrapVue, IconsPlugin } from "bootstrap-vue";

// Import Bootstrap and BootstrapVue CSS files (order is important)
import "bootstrap/dist/css/bootstrap.css";
import "bootstrap-vue/dist/bootstrap-vue.css";
import * as plotly from "https://cdn.plot.ly/plotly-2.11.1.min.js";

// Make BootstrapVue available throughout your project
Vue.use(BootstrapVue);
// Optionally install the BootstrapVue icon components plugin
Vue.use(IconsPlugin);

import * as d3 from "https://cdn.skypack.dev/d3@7";
import AOS from "aos";
import "aos/dist/aos.css";

var old_eurostat = {};

export default {
  name: "DJ_Project",
  components: { FirstPage, Home },
  data() {
    return {
      target_page: "",
      home: true,
      italia: false,
      piombino: false,
      regions_prod: [],
      minerals_prod: [],
      amount_prod: [],
      years_prod: [],

      eurostat_traces: [],

      energy_mix_eurostat: {},
      prod_mise_csv: {},
      stoc_centrals_mise_csv: {},
    };
  },
  mounted() {
    Promise.all([
      d3.csv("./static/produzione_ALL_PULITO.csv"),
      d3.csv("./static/centrali-stoccaggio_PULITO.csv"),
      d3.csv("./static/mix_energetico_eurostat.csv"),
    ]).then((csvs) => {
      //MISE
      //produzione idrocarburi
      this.regions_prod = csvs[0].map((d) => d.Regione);
      this.minerals_prod = csvs[0].map((d) => d.Minerale);
      this.amount_prod = csvs[0].map((d) => +d.Produzione);
      this.years_prod = csvs[0].map((d) => +d.Anno);
      this.prod_mise_csv = csvs[0];
      //stoccaggio idrocarburi (centrali)
      this.stoc_centrals_mise_csv = csvs[1];

      //Terna
      this.energy_mix_eurostat = csvs[2];
      old_eurostat = csvs[2];
    });
  },
  methods: {
    update_page(name) {
      this.target_page = "";
      this.target_page = name;

      if (this.target_page == "Italia") {
        this.italia = true;
        this.piombino = false;
        this.home = false;
        this.fonti = false;
      } else if (this.target_page == "Piombino") {
        this.piombino = true;
        this.italia = false;
        this.home = false;
        this.fonti = false;
      } else if (this.target_page == "Home") {
                this.home = true;
        setTimeout(() => {
          AOS.refresh();
        }, 500);
        AOS.init({ offset: 130, duration: 1000 });

        this.piombino = false;
        this.italia = false;
        this.fonti = false;

      } else if (this.target_page == "Fonti") {
        this.fonti = true;
        this.piombino = false;
        this.italia = false;
        this.home = false;
      }
    },
  },
};
/*** TEMP NAV: <b-nav-item id='first_nav_item' class="nav_item" @click="update_page('Italia')">Il problema Italia</b-nav-item>
          <b-nav-item class="nav_item"  @click="update_page('Piombino')">Il problema Piombino</b-nav-item>
           <b-nav-item class="nav_item" @click="update_page('Fonti')">Fonti</b-nav-item>
           <b-button id='nav_btn' class="nav_item" variant="primary" @click="console.log('pressed')" pill>Autori</b-button>
           
           ***/
// <FirstPage :mise_prod_data="this.prod_mise_csv" :mise_stoc_centrals="this.stoc_centrals_mise_csv"></FirstPage>
</script>

<template>
  <div id="app">
    <div>
      <b-navbar toggleable="lg" type="dark" variant="dark" id="navbar_">
        <b-navbar-nav>
          <b-navbar-brand class="nav_home" @click="update_page('Home')"
            >Quale energia per l'Italia</b-navbar-brand
          >
        </b-navbar-nav>
        <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

        <b-collapse id="nav-collapse" is-nav>
          <b-navbar-nav>
            <b-nav-item
              id="first_nav_item"
              class="nav_item"
              @click="update_page('Italia')"
              >Produzione nazionale</b-nav-item
            >

           
          </b-navbar-nav>
        </b-collapse>
      </b-navbar>
    </div>
    <Home
      v-if="home"
      :eurostat_="this.energy_mix_eurostat"
      :t_page="this.target_page"
    ></Home>
    <FirstPage
      v-if="italia"
      :mise_prod_data="this.prod_mise_csv"
      :mise_stoc_centrals="this.stoc_centrals_mise_csv"
    ></FirstPage>

    <h1 v-if="piombino">asdasd</h1>
  </div>
</template>

<style>
@import "./assets/base.css";

html {
  height: 100%;
}
body {
  background-color: #212529 !important;
  color: white !important;
}
#navbar_ {
  width: 100%;
  position: fixed;
  z-index: 998;
  min-height: 5rem;
  top:0;
  left:0
}
.nav_home {
  margin-left: 5vw;
}
.nav_item {
  margin-left: 10vw;
}
</style>

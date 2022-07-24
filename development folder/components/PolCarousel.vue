<script>
import * as d3 from "https://cdn.skypack.dev/d3@7";
export default {
  name: "PolCarousel",
  props: {},
  data() {
    return {
      pol_data: {},
      names: [],
      idx: [],
      affermazioni: [],
      date: [],
      fonti: [],
      urls: [],
      sliding: Boolean,
    };
  },
  mounted() {
    Promise.all([d3.csv("./static/citazioni_politici.csv")]).then((csvs) => {
      this.pol_data = csvs[0];
      this.names = csvs[0].map((d) => d.Nome);
      this.idx = csvs[0].map((i, d) => i);
    });
  },
  methods: {
    onSlideStart(slide) {
      this.sliding = true;
    },
    onSlideEnd(slide) {
      this.sliding = false;
    },
  },
};
</script>

<template>
  <div>
    <b-carousel
      id="carousel-1"
      v-model="slide"
      :interval="4000"
      controls
      indicators
      background="#ababab"
      img-width="1024"
      img-height="480"
      style="text-shadow: 1px 1px 2px #333"
      @sliding-start="onSlideStart"
      @sliding-end="onSlideEnd"
    >
      <!-- Text slides with image -->
      <b-carousel-slide
        img-src="./static/imgs/back.webp"
        class="w-100"
        alt="image"
        style="height: 20rem"
      ><b-img src="./static/imgs/m5s.png" style="width:5rem"></b-img>
      <p class="carousel-text">"Estrarre più gas dagli impianti esistenti e dai rigassificatori galleggianti."</p>
        <h3 class="carousel-pg">Conte - M5S</h3>
        <h6 class="carousel-src">
          Fonte:
          <a
            href="https://www.ilgiornale.it/news/politica/ennesima-giravolta-5-stelle-conte-bisogna-estrarre-pi-gas-2018357.html"
            >Il Giornale</a
          >
          - 16/03/2022
        </h6></b-carousel-slide
      >

            <b-carousel-slide
        img-src="./static/imgs/back.webp"
        class="w-100"
        alt="image"
        style="height: 20rem"
      ><b-img src="./static/imgs/fdi.png" style="width:5rem"></b-img>
      <p class="carousel-text">"È chiaro che un problema energetico c’è, ma se ci sono alternative (n.d.r. al terminal gas) devono dirci quali, se non ci sono devono dimostrarcelo."</p>
        <h3 class="carousel-pg">Meloni - FdI</h3>
        <h6 class="carousel-src">
          Fonte:
          <a
            href="https://www.iltelegrafolivorno.it/cronaca/meloni-giusto-il-no-al-terminal-gas-1.7754405"
            >Il Telegrafo</a
          >
          - 06/06/2022
        </h6></b-carousel-slide
      >


            <b-carousel-slide
        img-src="./static/imgs/back.webp"
        class="w-100"
        alt="image"
        style="height: 20rem"
      ><b-img src="./static/imgs/ipf.png" style="width:5rem"></b-img>
      <p class="carousel-text">"È necessario produrre più petrolio e gas, servono misure straordinarie."</p>
        <h3 class="carousel-pg">Di Maio - Ipf</h3>
        <h6 class="carousel-src">
          Fonte:
          <a
            href="https://www.tgcom24.mediaset.it/mondo/crisi-energetica-di-maio-necessario-produrre-piu-petrolio-e-gas_47331381-202202k.shtml"
            >TgCom24</a
          >
          - 14/03/2022
        </h6></b-carousel-slide
      >

            <b-carousel-slide
        img-src="./static/imgs/back.webp"
        class="w-100"
        alt="image"
        style="height: 20rem"
      ><b-img src="./static/imgs/eu.png" style="width:5rem"></b-img>
      <p class="carousel-text"> "Accelerare sull'installazione dei rigassificatori a Piombino (n.d.r. e a Ravenna) entro la prossima primavera."</p>
        <h3 class="carousel-pg">Draghi - Indipendente</h3>
        <h6 class="carousel-src">
          Fonte:
          <a
            href="https://www.lanazione.it/politica/nardella-sul-rigassificatore-l-infrastruttura-serve-draghi-ha-ragione-1.7902716"
            >La Nazione</a
          >
          - 16/03/2022
        </h6></b-carousel-slide
      >

            <b-carousel-slide
        img-src="./static/imgs/back.webp"
        class="w-100"
        alt="image"
        style="height: 20rem"
      ><b-img src="./static/imgs/azione.png" style="width:5rem"></b-img>
      <p class="carousel-text">"Occorre raggiungere in 12 mesi l’indipendenza dal gas russo anche riattivando temporaneamente tutta la potenza installata a carbone e completando rigassificatori."</p>
        <h3 class="carousel-pg">Calenda - Azione </h3>
        <h6 class="carousel-src">
          Fonte:
          <a
            href="https://twitter.com/carlocalenda/status/1520426706326196229"
            >Twitter</a
          >
          - 30/04/2022
        </h6></b-carousel-slide
      >




      <!-- Slides with custom text -->
    </b-carousel>
  </div>
</template>

<style>
.carousel-text{
  color:black;
}
.carousel-pg{
  color:black;
}
.carousel-src{
  color:black;
}

#carousel-1___BV_indicator_1_,#carousel-1___BV_indicator_2_,#carousel-1___BV_indicator_3_,#carousel-1___BV_indicator_4_,#carousel-1___BV_indicator_5_{
  background-color:grey;
}


</style>
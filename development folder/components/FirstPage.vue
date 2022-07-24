<script>
import Map from "./Map.vue";
import MiseProdPlot from "./MiseProdPlot.vue";
export default {
  name: "FirstPage",
  props: {
    mise_prod_data: Object,
  },
  components: { Map, MiseProdPlot },
  data() {
    return {
      option_layers: [
        "Gasdotti",
        "PITESAI",
        "Centrali stoccaggio",
        "Pozzi storici",
        "Centrali termoelettriche",
      ],
      selected_layers:"Centrali stoccaggio",

      opt_central_stoc_color: ["---", "Operatore", "Minerale"],
      sel_central_stoc_color: "Operatore",
      opt_central_stoc_size: ["---", "Area(m2)", "N. pozzi"],
      sel_central_stoc_size: "Area(m2)",

      opt_central_term_color: ["---", "Operatore", "Stato"],
      sel_central_term_color: "Operatore",
      opt_central_term_size: ["---", "Potenza(MW)"],
      sel_central_term_size: "Potenza(MW)",

      opt_pozzi_idr_color: [
        "---",
        "Centrale",
        "Minerale",
        "Operatore",
        "Sezione",
        "Stato",
      ],
      sel_pozzi_idr_color: "Centrale",

      opt_pozzi_stor_color:
        "---,Scopo,Esito,Tipo titolo,Provincia/Zona,Stato,Anno"
          .split(",")
          .sort(),
      sel_pozzi_stor_color: "Stato",
      opt_pozzi_stor_size: ["---", "Profondità"],
      sel_pozzi_stor_size: "Profondità",

      opt_pozzi_stoc_color: [
        "---",
        "Minerale",
        "Operatore",
        "Sezione",
        "Stato",
      ],
      sel_pozzi_stoc_color: "Minerale",
      sel_type_: "Singolo",
      start_:true
    };
  },
  mounted() {
    this.option_layers.sort();
  },
  methods: {
    filter_layers() {
      //console.log("SELECTED", this.selected_layers);
    },
  },
  watch: {
    option_layers: function (newValue) {
      
    },
  },
};
</script>

<template>
  <b-container>
<br/><br/><br/><br/><br/>
        <div class="section"> 
        <h1>Come consultare i dati</h1>
        <div class="body_section_center">
          Nella mappa di calore è possibile osservare la correlazione fra data e territori nelle estrazioni di idrocarburi sul suolo italiano.
          <br/> I dati della mappa di calore sono stati scalati utilizzando la radice quadrata.
          <br/> Inoltre è possibile osservare isolatamente le due dimensioni (spazio e tempo, queste scalate logaritmicamente) grazie alle distribuzioni marginali: 
          <ul>
            <li>il grafico a linea al di sopra della mappa di calore per quanto riguarda l'asse x (rappresentando dunque l'andamento delle estrazioni solo nel tempo, ignorando la località)</li>
            <li> e il grafico a barre alla destra della mappa di calore, rappresentante l'estrazione in base ai soli territori (escludendo dunque il tempo).</li>
          </ul>
          Ricordiamo inoltre che le Zone (A, B, C, D, F) rappresentano le <a href="https://unmig.mise.gov.it/index.php/it/dati/cartografia/zone-marine-aperte-alla-ricerca-e-coltivazione-di-idrocarburi">zone marine aperte alla ricerca e coltivazione di idrocarburi</a>.
          <br/>
          Per consultare i dati, naviga al <a href="https://unmig.mise.gov.it/index.php/it/dati/ricerca-e-coltivazione-di-idrocarburi/produzione-nazionale-di-idrocarburi">sito del MISE.</a>
        </div>

        </div>
        <h2 id="title_firstpage">Produzione idrocarburi (kg)</h2>

    <MiseProdPlot></MiseProdPlot>

        <div class="section"> 
        <h1>Come consultare la mappa</h1>
        <div class="body_section_center">
         La seguente interfaccia consiste in una serie di filtri pensati per lasciare all'utente libertà di ricerca. In particolare:
         <ul style="text-align:left">
          <li><b>Selezione: </b>'Singolo' per selezionare un livello alla volta, 'Multiplo' per poterne selezionare più di uno (CTRL+click destro sui livelli desiderati, sia per selezionare che per deselezionare)</li>
          <li><b>Livelli: </b> filtro per decidere quali livelli mostrare su mappa<br/><a style="margin-left:5rem"> (<b>ATTENZIONE:</b> il livello <b>"Pozzi storici"</b>, contenente circa 9000 elementi, è <b>lento</b> nel caricare)</a></li>
          <li><b>Legenda:</b> filtro per decidere quale legenda mostrare (utile nel caso di selezione multipla)</li>
          <li><b>Colore e dimensione (TABS):</b> Ogni tab rappresenta un singolo livello, in ciascuna tab è possibile decidere che dimensione assegnare al colore e alla grandezza dei punti (es. colore=Operatore; grandezza=Area(m2))</li>
          <li><b>Lista punti: </b> Clicca sul pulsante in alto a sinistra sulla mappa per nascondere/mostrare la lista dei punti</li>
         </ul>
        </div>

        </div>

    <div style="background-color:rgba(220,220,220,0.2);border:groove black 2px;width:10rem">
    <code>Selezione</code>
    <b-form-group>
      <b-form-radio-group v-model="sel_type_"
>
        <b-form-radio
          name="Singolo"
          value="Singolo"
          id="sel_singolo">Singolo</b-form-radio
        ><b-form-radio
          name="Multiplo"
          value="Multiplo"
          id="sel_multiplo"
          >Multiplo</b-form-radio
        >
      </b-form-radio-group>
    </b-form-group>
    </div>

  

    <b-row>
      <div  style="background-color:rgba(220,220,220,0.2);border:groove black 2px;margin:0.1rem 0rem 0.1rem 1rem;width:16rem">
      <b-col id="lev_">
        <code>Livelli</code>
        <b-form-select
          id="layer_selection"
          v-model="selected_layers"
          :options="option_layers"
          v-on:change="filter_layers"
        ></b-form-select>
      </b-col>
      </div>
      <b-col
        ><div>
          <b-tabs content-class="mt-3" id="tabs_" style="background-color:rgba(220,220,220,0.2);border:groove black 2px">
            <b-tab id="tab_centrali_stoc" title="Centrali stoccaggio" active>
              <b-container>
                <b-row>
                  <b-col>
                    <code>Colore</code>
                    <b-form-select
                      v-model="sel_central_stoc_color"
                      :options="opt_central_stoc_color"
                    ></b-form-select
                  ></b-col>
                  <b-col>
                    <code>Dimensione</code>
                    <b-form-select
                      v-model="sel_central_stoc_size"
                      :options="opt_central_stoc_size"
                    ></b-form-select
                  ></b-col>
                </b-row>
              </b-container>
            </b-tab>
            <b-tab id="tab_centrali_term" title="Centrali termoelettriche">
              <b-container>
                <b-row>
                  <b-col>
                    <code>Colore</code>
                    <b-form-select
                      v-model="sel_central_term_color"
                      :options="opt_central_term_color"
                      v-on:change="filter_layers"
                    ></b-form-select
                  ></b-col>
                  <b-col>
                    <code>Dimensione</code>
                    <b-form-select
                      v-model="sel_central_term_size"
                      :options="opt_central_term_size"
                      v-on:change="filter_layers"
                    ></b-form-select
                  ></b-col>
                </b-row> </b-container
            ></b-tab>
            <b-tab id="tab_pozzi_idr" title="Pozzi storici"
              ><b-container>
                <b-row>
                  <b-col>
                    <code>Colore</code>
                    <b-form-select
                      v-model="sel_pozzi_stor_color"
                      :options="opt_pozzi_stor_color"
                      v-on:change="filter_layers"
                    ></b-form-select
                  ></b-col>
                  <b-col>
                    <code>Dimensione</code>
                    <b-form-select
                      v-model="sel_pozzi_stor_size"
                      :options="opt_pozzi_stor_size"
                      v-on:change="filter_layers"
                    ></b-form-select
                  ></b-col>
                </b-row> </b-container
            ></b-tab>
          </b-tabs></div
      ></b-col>
    </b-row>
    <Map
      id="meta_map"
      :filters="[
        [sel_central_stoc_color, sel_central_stoc_size],
        [sel_central_term_color, sel_central_term_size],
        [sel_pozzi_stor_color, sel_pozzi_stor_size],
      ]"
      :layers="[this.selected_layers].flat()"
      :sel_type="this.sel_type_"
      :start="start_"
    ></Map>
  </b-container>
</template>


<style>
#tabs_{
  margin-top:-3.8rem
}
#title_firstpage{
  margin-left:25rem
}

#header_{
  margin-top:3em
}
</style>

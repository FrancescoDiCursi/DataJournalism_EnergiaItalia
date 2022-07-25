<script>
import * as d3 from "https://cdn.skypack.dev/d3@7";
import PiesEurostat from "./PiesEurostat.vue";
import LineEurostat from "./LineEurostat.vue";
import LineImports from "./LineImports.vue";

import GasConsumptionD3 from "./GasConsumptionD3.vue";
import RenewableD3 from "./RenewableD3.vue";

import PolCarousel from "./PolCarousel.vue";

import AOS from "aos";
import "aos/dist/aos.css";
import PiesEurostat1 from "./PiesEurostat.vue";
setTimeout(() => {
  AOS.refresh();
}, 500);
AOS.init({ offset: 130, duration: 1000 });

//CHANGE SIZE OF WINDOW (to make aos work on first load; now it works only if F12 is pressed)
export default {
  name: "Home",
  components: {
    PiesEurostat,
    LineEurostat,
    LineImports,
    GasConsumptionD3,
    RenewableD3,
    PolCarousel,
    PiesEurostat1,
  },
  props: { eurostat_: Object, t_page: String },
  data() {
    return {};
  },
  mounted() {},
  methods: {
    go_to_map() {
      this.$parent.update_page("Italia");
    },
    handle_index(idx) {
      var el = document.getElementById(idx);
      el.scrollIntoView({ behavior: "smooth" });
    },

    pie_mix_eurostat(data) {
      var data_ = [];
      var domains = [0, 1];
      var names = [];
      for (var data of this.eurostat_) {
        var keys = Object.keys(data);
        var values = Object.values(data);
        var name = values[0];

        names.push(name);

        keys = keys.slice(2);
        values = values.slice(2);


        var data_temp = {
          values: values.map((d) => d), //values.map(d=>d.replace('.','').replace(',','.')),
          labels: keys.map((d) => d),
          name: name,
          hoverinfo: "label+percent+name",
          domain: { column: [name].map((d) => (d == "Italy" ? 1 : 0))[0] },
          hole: 0.3,
          type: "pie",
        };

        data_.push(data_temp);
      }


      var layout = {
        title: "Bilancio energetico (EUROSTAT, 2020)",
        annotations: [
          {
            font: {
              size: 20,
            },
            showarrow: false,
            text: "EU(27)",
            x: 0.204,
            y: 0.5,
          },
          {
            font: {
              size: 20,
            },
            showarrow: false,
            text: "Italy",
            x: 0.785,
            y: 0.5,
          },
        ],

        showlegend: true,
        legend: {
          x: 0.37,
          y: 1,
          orientation: "vertical",
          bgcolor: "rgba(0,0,0,0)",
        },
        grid: { rows: 1, columns: 2 },
        paper_bgcolor: "rgba(0,0,0,0)",
        plot_bgcolor: "rgba(0,0,0,0)",
        font: {
          //family: 'Courier New, monospace',
          //size: 18,
          color: "white",
        },
      };

      Plotly.newPlot("plot_section_1", data_, layout);
    },
  },

  watch: {},
};
//SIDEBAR VECCHIA
/*** 
      <div>
    <b-button v-b-toggle.sidebar-footer id="menu_toggle">Mostra indice</b-button>
    <b-sidebar id="sidebar-footer" aria-label="Sidebar with custom footer" no-header shadow>
      <template #footer="{ hide }">
       <div class="d-flex bg-dark text-light align-items-center px-3 py-2">
        <strong class="mr-auto">Indice</strong>
        <b-button size="sm" @click="hide">Nascondi indice</b-button>
       </div>
      </template>
      <div class="px-3 py-2">
       <ul>
        <li><b-button class="idx_els" @click="handle_index('first_section')">First section</b-button></li>
        <li><b-button class="idx_els" @click="handle_index('second_section')">Second section</b-button></li>
        <li><b-button class="idx_els" @click="handle_index('third_section')">Third section</b-button></li>
       </ul>
       
      </div>
    </b-sidebar>
  </div>
  ***/
</script>

<template>
  <span>
    <div>
      <b-button v-b-toggle.sidebar-footer id="menu_toggle"
        >Mostra indice</b-button
      >
      <b-sidebar
        id="sidebar-footer"
        aria-label="Sidebar with custom footer"
        no-header
        shadow
      >
        <template #footer="{ hide }">
          <div class="d-flex bg-dark text-light align-items-center px-3 py-2">
            <strong class="mr-auto">Indice</strong>
            <b-button size="sm" @click="hide">Nascondi indice</b-button>
          </div>
        </template>
        <div class="px-3 py-2">
          <ul id="index">
            <li>
              <b-button class="idx_els" @click="handle_index('first_section')"
                >Il rigassificatore di Piombino</b-button
              >
            </li>
            <li>
              <b-button class="idx_els" @click="handle_index('second_section')"
                >Italia e i nuovi rigassificatori</b-button
              >
            </li>
            <li>
              <b-button class="idx_els" @click="handle_index('third_section')"
                >Come arriva il gas in Italia</b-button
              >
            </li>
            <li>
              <b-button class="idx_els" @click="handle_index('fourth_section')"
                >Problematiche Piombino</b-button
              >
            </li>
            <li>
              <b-button class="idx_els" @click="handle_index('fifth_section')"
                >Politica e propaganda</b-button
              >
            </li>
            <li>
              <b-button class="idx_els" @click="handle_index('sixth_section')"
                >Abbandonare fonti fossili</b-button
              >
            </li>
            <li>
              <b-button class="idx_els" @click="handle_index('seventh_section')"
                >Indietro con le rinnovabili</b-button
              >
            </li>
          </ul>
        </div>
      </b-sidebar>
    </div>
    <div
      class="bd-content col-md-9 col-xl-8 col-12 pb-md-3 pl-md-5"
      id="article_body"
    >
      <main class="bd-content" data-v-a6bd3544>
        <section class="bd-content" data-v-a6bd3544>
          <div class="section" id="first_section" data-aos="fade-up">
            <h1 class="title_section"></h1>
            <div class="body_section body_section_center"></div>
            <section
              data-aos="fade-left"
              class="py-4 py-xl-5 aos-init aos-animate"
            >
              <div class="container">
                <div
                  class="
                    bg-dark
                    border
                    rounded
                    border-0 border-dark
                    overflow-hidden
                  "
                >
                  <h2 class="fw-bold text-white mb-3">
                    <br /><strong
                      ><span style="background-color: rgb(45, 44, 56)">
                        <h3>Il rigassificatore di Piombino</h3></span
                      ></strong
                    >
                  </h2>
                  <div class="row g-0">
                    <div class="col-md-6">
                      <div class="text-white p-4 p-md-5">
                        <p class="mb-4">
                          <br /><span style="background-color: rgb(45, 44, 56)">
                            <p>
                              Il rigassificatore di Piombino sarà una nave lunga
                              300 metri e larga 40: la Golar Tundra.
                            </p>
                            <br />
                            <p>
                              La nave-rigassifcatore o FSRU (Floating Storage
                              Regasification Unit) sarà ormeggiata per tre anni
                              nel porto cittadino dove riceverà le altre navi
                              trasportatrici di GNL per rifornirla. Il gas
                              naturale liquefatto occupa un volume circa 600
                              volte inferiore rispetto a quando si trova allo
                              stato aeriforme e di conseguenza una nave
                              metaniera può contenere quantità molto maggiori.
                              Il trasporto avviene all’interno di grandi
                              cisterne a una pressione poco superiore a quella
                              atmosferica, ma a una temperatura di -162 °C.
                            </p>
                            <p>
                              Nei rigassificatori, il GNL torna allo stato
                              gassoso grazie a un processo di riscaldamento, e
                              viene trasferito subito all’interno di un impianto
                              che ha un volume adatto all’espansione del gas.
                              Infine il gas viene immesso nella rete e
                              distribuito nelle abitazioni, nelle aziende e
                              utilizzato nelle centrali a gas per la produzione
                              di energia elettrica. Secondo la SNAM, la Golar
                              Tundra erogherà 5 miliardi di metri cubi di gas
                              all’anno, riducendo così la quantità di gas
                              acquistato dalla Russia. Inoltre il GNL permette
                              di diversificare l’approvvigionamento di gas
                              rispetto ai tradizionali gasdotti, strutture fisse
                              che permettono l’importazione del gas solo dai
                              paesi dove è collegata la rete di distribuzione.
                            </p>
                            <br /><br />
                            <p>
                              Queste proteste infatti vertono principalmente su
                              due aspetti:
                            </p>
                            <ul>
                              <li>
                                La Golar Tundra quando è in funzione deve
                                scaricare circa 50 chilogrammi di cloro al
                                giorno e le conseguenze sull’ecosistema marino
                                non sono ancora chiare e necessitano di studi
                                specifici, come aveva spiegato recentemente il
                                docente di Ecologia al dipartimento di Biologia
                                dell’Università di Pisa, Alberto Castelli:
                                «Bisogna considerare il fatto che, come
                                sostanza, il cloro nell’acqua già sia presente
                                in maniera naturale, resta da capire in che
                                forma e quantità viene immesso, dato che
                                esistono dei limiti scritti da non superare, che
                                immediatamente farebbero capire l’invasività
                                dell’operazione». Infatti, i pescatori
                                piombinesi temono danni permanenti per i loro
                                allevamenti di cozze e di pesce, tra i primi in
                                Italia.
                              </li>
                              <br />
                              <li>
                                Siccome la nave dovrà collegarsi al punto di
                                accesso della rete di distribuzione del gas
                                nazionale sulla terraferma, è necessaria
                                l’installazione di un tubo lungo 8 chilometri.
                                Il tubo passerà nell’area industrializzata di
                                Piombino e dovrà essere realizzato o sottoterra,
                                rendendo necessarie attività di bonifica e messa
                                in sicurezza del suolo, oppure a cielo aperto
                                con un certo impatto per le attività nell’area
                                interessata. I cittadini sono preoccupati per
                                eventuali guasti del gasdotto per la vicinanza
                                di una delle vie di accesso principali alla
                                città.
                              </li>
                            </ul>
                            <br /> </span
                          ><br /><br />
                        </p>
                      </div>
                    </div>
                    <div
                      class="col-md-6 order-first order-md-last"
                      style="min-height: 250px"
                    >
                      <b-img
                        class="
                          rounded
                          img-fluid
                          w-100
                          h-100
                          fit-cover
                          aos-init aos-animate
                        "
                        data-aos="fade-right"
                        src="./static/imgs/Cream And Pink Abstract Illustrated Travel Alone Tips Infographic.png"
                        width="300"
                        height="500"
                      />
                    </div>
                  </div>
                </div>
              </div>
              <div class="body_section_center">
                I vari schieramenti politici locali si sono schierati contro il
                rigassificatore, andando contro i propri gruppi nazionali che
                sono a favore dell’opera. Il sindaco di Piombino, Francesco
                Ferrari che guida il fronte del no, ha criticato il presidente e
                commissario Giani, per non aver coinvolto adeguatamente le
                amministrazioni locali su una decisione così cruciale. Per
                adesso si sta solo ipotizzando l’organizzazione di un tavolo di
                lavoro che coinvolga tutte le parti in campo per verificare
                l’andamento dei lavori e che valuti le richieste dei cittadini,
                in particolare sulla sicurezza ambientale.
              </div>
            </section>
          </div>
          <div class="section" id="second_section" data-aos="fade-up">
            <h1 class="title_section">
              Ma perché l’Italia ha bisogno di nuovi rigassificatori?
            </h1>
            <div class="body_section body_section_center">
              Il punto è che l’Italia non è energeticamente indipendente e per
              far funzionare tutto il sistema paese deve importare energie
              fossili dall’estero.
            </div>
            <div class="plot_section">
              <PiesEurostat></PiesEurostat>
            </div>

            <div class="body_section body_section_center">
              Come si può vedere dal grafico, il mix energetico Italiano nel
              2020 è stato composto da un 77% di combustibili fossili, in
              particolare dal gas naturale. L’Italia purtroppo non ha grandi
              riserve, secondo i dati del Mise nel sottosuolo Italiano ci sono
              solo 39 miliardi di metri cubi di gas che si possono estrarre.
              Sembra una gran quantità ma annualmente consumiamo all’incirca 70
              miliardi di metri cubi di gas, questo vuol dire che se
              consumassimo tutte le nostre riserve in un anno copriremmo solo il
              55% del fabbisogno nazionale. Per chi voglia approfondire i siti
              di estrazione che ci sono in Italia consigliamo di esplorare i
              dati del Mise nella
              <b-button @click="go_to_map" id="gotomap_btn"
                >seguente sezione</b-button
              >. Ricordiamo che il PITESAI, ossia il Piano della transizione
              energetica sostenibile delle aree idonee varato dal primo Governo
              Conte, del 2019 ha ridotto il numero di giacimenti che si possono
              sfruttare a causa di nuovi limiti burocratici.
            </div>

            <div class="plot_section">
              <GasConsumptionD3></GasConsumptionD3>
            </div>

            <div class="body_section body_section_center">
              In questo articolo ci concentreremo solo sul gas, essendo il
              rigassificatore il nostro principale tema. Ma non va dimenticato
              che lo stesso problema di dipendenza esiste anche per materie
              prime come petrolio e carbone, l’Italia è costretta a importarli
              dall’estero, in particolare dalla Russia.
            </div>
            <br /><br />
          </div>

          <b-container>
            <div class="section">
              <h1 class="title_section" id="third_section" data-aos="fade-up">
                COME ARRIVA IL GAS IN Italia
              </h1>

              <div class="body_section_center" data-aos="fade-up">
                L'Italia importa il gas da vari paesi extra UE.
                <p>
                  Escludendo gli Stati Uniti (2,6%) e la Norvegia (11,1%) tutti
                  gli altri paesi hanno una situazione politica molto delicata.
                  Il problema oggi è che per via del conflitto ucraino, l'Italia
                  si è schierata apertamente contro la federazione russa che da
                  sola ci rifornisce di quasi il 43 % del nostro fabbrisogno di
                  gas. Ci troviamo in uno stato di emergenza e non è solo un
                  problema dell’Italia, tutta l’Europa in questi ultimi decenni
                  ha investito molto nel creare una capillare rete di
                  rifornimento di gas.
                </p>
                <p>
                  L’opinione pubblica parla spesso di come si possa
                  assolutamente arricchire un dittatore come Vladimir Putin e
                  finanziare un’aggressione ingiusta contro l’Ucraina. In realtà
                  storicamente, tra i nostri fornitori di gas naturale ci sono
                  sempre stati regimi poco democratici come Libia, Algeria e
                  Qatar. Questi paesi non si trovano certamente in una
                  situazione di assoluta stabilità politica e secondo il
                  Democracy Index del 2020 sono paesi con un posto in classifica
                  molto più vicino alla russia (124°): Algeria 113°, Qatar 114°,
                  Libia 154°
                </p>
              </div>
              <div
                class="plot_section"
                data-aos="fade-left"
                data-aos-delay="50"
              >
                <b-img
                  src="static/imgs/plot_prova_5.png"
                  style="width: 50%"
                ></b-img>
              </div>
            </div>
          </b-container>
          <b-container>
            <b-row>
              <b-col class="cols_var"
                ><div class="plot_section" data-aos="fade-right">
                  <a
                    href="https://commons.wikimedia.org/wiki/File:Yamal-europe.png#/media/File:Yamal-europe.png"
                    ><img
                      src="https://upload.wikimedia.org/wikipedia/commons/thumb/7/74/Yamal-europe.png/1200px-Yamal-europe.png"
                      alt="Location of Yamal–Europe pipeline"
                      style="width: 30rem"
                  /></a>
                  <br />By Samuel Bailey (sam.bailus@gmail.com)
                  <a
                    href="https://creativecommons.org/licenses/by-sa/3.0"
                    title="Creative Commons Attribution-Share Alike 3.0"
                    >CC BY-SA 3.0</a
                  >,
                  <a
                    href="https://commons.wikimedia.org/w/index.php?curid=8462300"
                    >Link</a
                  >
                </div></b-col
              >
              <b-col class="cols_var"
                ><h1 class="title_section" data-aos="fade-right">
                  Il Sistema Yamal-Europe
                </h1>
                <div
                  class="body_section"
                  data-aos="fade-left"
                  data-aos-delay="50"
                >
                  Il gasdotto lungo 4500 km attraversa molti stati europei e
                  l’ultimo a essere rifornito è proprio l'Italia. Il fatto che
                  passi anche attraverso l’Ucraina rende più probabile in
                  problema della sospensione delle forniture. Ora, per motivi
                  geopolitici tutta europa sta cercando di sostituire il gas
                  russo in vista del prossimo inverno, l’europa applica sanzioni
                  contro la Russia che reagisce diminuendo senza largo preavviso
                  le proprie forniture.
                </div>
              </b-col>
            </b-row>
          </b-container>
          <b-container>
            <b-row>
              <b-col class="cols_var"
                ><h1 class="title_section" data-aos="fade-right">
                  Il Transmed e il gas algerino
                </h1>
                <div class="body_section" data-aos="fade-right">
                  il Gas algerino ci copre all'incirca un altro 30% del
                  fabrisogno nazionale. Per arrivare da noi però attraversa un
                  altro stato, la tunisia che recentemente sta avendo una
                  <a
                    href="https://www.affarinternazionali.it/saied-tunisia-via-autoritaria/"
                    >deriva autoritaria</a
                  >poiché il presidente sta cercando di accentrare a sé più
                  poteri possibili.
                </div>
              </b-col>
              <b-col class="cols_var"
                ><div class="plot_section" data-aos="fade-right">
                  <b-img
                    src="./static/imgs/transmed.png"
                    style="width: 25rem"
                  ></b-img
                  ><br />
                </div>
                <a
                  href="https://commons.wikimedia.org/wiki/File:Gas_pipelines_across_Mediterranee_and_Sahara_map-en.svg#/media/Datei:Gas_pipelines_across_Mediterranee_and_Sahara_map-en.svg"
                  >Von © Sémhur&nbsp;/&nbsp;<a
                    href="//commons.wikimedia.org/wiki/Main_Page"
                    title="Main Page"
                    >Wikimedia Commons</a
                  >,
                  <a
                    href="https://creativecommons.org/licenses/by-sa/4.0"
                    title="Creative Commons Attribution-Share Alike 4.0"
                    >CC BY-SA 4.0</a
                  >,
                  <a
                    href="https://commons.wikimedia.org/w/index.php?curid=7309696"
                    >Link</a
                  >
                </a></b-col
              >
            </b-row>
          </b-container>
          <b-container>
            <b-row>
              <b-col class="cols_var">
                <div class="plot_section" data-aos="fade-left">
                  <b-img
                    src="static/imgs/greenstream.png"
                    style="width: 28rem"
                  ></b-img>
                </div>
              </b-col>
              <b-col class="cols_var">
                <h1 class="title_section" data-aos="fade-left">
                  Il Green Stream
                </h1>
                <div class="body_section" data-aos="fade-left">
                  Si chiama così il gasdotto sottomarino che collega l’Italia
                  alla Libia, costruito durante il regime del dittatore
                  Gheddafi. La guerra civile attuale e la mancanza di un
                  referente autorevole crea non pochi problemi all’Italia che al
                  momento appoggia il governo militare di Al-Sarraj. Fazione che
                  attualmente sembra destinata a perdere la guerra civile e
                  anche in questo caso sembra di aver puntato sul cavallo
                  sbagliato.
                </div>
              </b-col>
            </b-row>
          </b-container>
          <b-container>
            <b-row>
              <b-col class="cols_var"
                ><h1 class="title_section" data-aos="fade-right">
                  Il famigerato TAP
                </h1>
                <div class="body_section" data-aos="fade-right">
                  Forse il gasdotto più famoso, è stato al centro dell'
                  attenzione mediatica dal 2011 al 2017. Collega il nostro paese
                  all' Azerbaigian che rimane pur sempre un paese con forti
                  legami con la Russia e con una situazione interna abbastanza
                  delicata nel Nagorno Karabakh. Il Tap potrebbe essere
                  potenziato ma serve tempo per installare compressori più
                  potenti che possano aumentare la portata. Divenne famoso per
                  l’impatto paesaggistico che ha avuto sulla Puglia, essendo
                  stato fatto passare attraverso le campagne e le spiagge dal
                  forte richiamo turistico. I pugliesi erano scontenti anche dal
                  trattamento che sarebbe stato riservato agli ulivi presenti
                  sulla zona del passaggio degli scavi per il gasdotto.
                </div>
              </b-col>
              <b-col class="cols_var"
                ><div class="plot_section" data-aos="fade-right">
                  <a
                    href="https://commons.wikimedia.org/wiki/File:Trans_Adriatic_Pipeline.png#/media/File:Trans_Adriatic_Pipeline.png"
                    ><img
                      src="https://upload.wikimedia.org/wikipedia/commons/2/21/Trans_Adriatic_Pipeline.png"
                      alt="Trans Adriatic Pipeline.png"
                      style="width: 30rem" /></a
                  ><br />Di Genti77,
                  <a
                    href="https://creativecommons.org/licenses/by-sa/3.0"
                    title="Creative Commons Attribution-Share Alike 3.0"
                    >CC BY-SA 3.0</a
                  >,
                  <a
                    href="https://commons.wikimedia.org/w/index.php?curid=20669840"
                    >Collegamento</a
                  >
                </div>
              </b-col>
            </b-row>
          </b-container>
          <b-container>
            <b-row>
              <b-col class="cols_var">
                <div class="plot_section" data-aos="fade-left">
                  <b-img src="static/imgs/transitgas.png"></b-img>
                </div>
              </b-col>
              <b-col class="cols_var">
                <h1 class="title_section" data-aos="fade-left">
                  Il Transitgas
                </h1>
                <div class="body_section" data-aos="fade-left">
                  Viene chiamato così il gasdotto che arriva in Piemonte
                  partendo dall’Olanda e che ci rifornisce indirettamente di gas
                  norvegese. Questo approvvigionamento è quello più stabile e
                  meno soggetto alle variabili geopolitiche.
                </div>
              </b-col>
            </b-row>
          </b-container>
          <b-container>
            <b-row>
              <b-col class="cols_var"
                ><h1 class="title_section" data-aos="fade-right">
                  Le navi GNL
                </h1>
                <div class="body_section" data-aos="fade-right">
                  L’ultimo modo in cui il gas arriva in Italia è grazie alle
                  navi metaniere che approdano in punti di rigassificazione. I
                  rigassificatori sono impianti che ritrasformano nello stato
                  gassoso il gas, infatti era stato trasformato in liquido
                  perchè occupava meno spazio. Gli impianti di rigassificazione
                  possono essere fissi o galleggianti(FSRU). Gli impianti di
                  rigassificazione in Italia sono tre: a Livorno, a La spezia e
                  Porto Viro.
                </div>
              </b-col>
            </b-row>

            <b-row>
              <b-col class="cols_var">
                <div class="body_section" data-aos="fade-right">
                  <ul>
                    <li>
                      Il più grande è il Terminale GNL Adriatico ed è un
                      impianto offshore, un’isola artificiale che si trova in
                      mare al largo di Porto Viro, in Veneto e ha una capacità
                      di produzione annuale di 8 miliardi di metri cubi di gas.
                    </li>
                  </ul>
                </div>
              </b-col>
              <b-col class="cols_var"
                ><div class="plot_section" data-aos="fade-right">
                  <p>
                    <a
                      href="https://commons.wikimedia.org/wiki/File:Lng_adriatic.jpg#/media/File:Lng_adriatic.jpg"
                      ><img
                        src="https://upload.wikimedia.org/wikipedia/commons/thumb/7/70/Lng_adriatic.jpg/1200px-Lng_adriatic.jpg"
                        alt="Lng adriatic.jpg"
                        style="width: 30rem" /></a
                    ><br />Di Floydrosebridge,
                    <a
                      href="https://creativecommons.org/licenses/by-sa/3.0"
                      title="Creative Commons Attribution-Share Alike 3.0"
                      >CC BY-SA 3.0</a
                    >,
                    <a
                      href="https://commons.wikimedia.org/w/index.php?curid=18935692"
                      >Collegamento</a
                    >
                  </p>
                </div>
              </b-col>
            </b-row>

            <b-row>
              <b-col class="cols_var">
                <div class="body_section" data-aos="fade-right">
                  <ul>
                    <li>
                      Il secondo, anch’esso offshore, si trova al largo della
                      costa tirrenica di Livorno: è una nave metaniera
                      modificata e ancorata in maniera definitiva al fondale, in
                      attività dal 2013. La sua capacità è di 3,75 miliardi di
                      metri cubi annui.
                    </li>
                  </ul>
                </div>
              </b-col>
              <b-col class="cols_var"
                ><div class="plot_section" data-aos="fade-right">
                  <b-img
                    src="./static/imgs/secondorigass.png"
                    style="width: 30rem"
                  ></b-img>
                </div>
              </b-col>
            </b-row>

            <b-row>
              <b-col class="cols_var">
                <div class="body_section" data-aos="fade-right">
                  <ul>
                    <li>
                      Il terzo rigassificatore si trova sulla terraferma infatti
                      è una struttura onshore: è situato in Liguria a
                      Panigaglia, in provincia di La Spezia e la sua capacità di
                      trattamento è di 3,5 miliardi di metri cubi di gas
                      all’anno. Come per piombino questo impianto è molto vicino
                      a un centro abitato
                    </li>
                  </ul>
                </div>
              </b-col>
              <b-col class="cols_var"
                ><div class="plot_section" data-aos="fade-right">
                  <p>
                    <a
                      href="https://it.wikipedia.org/wiki/File:Snampanigaglia.jpg#/media/File:Snampanigaglia.jpg"
                      ><img
                        src="https://upload.wikimedia.org/wikipedia/it/7/71/Snampanigaglia.jpg"
                        alt="Snampanigaglia.jpg"
                        style="width: 30rem" /></a
                    ><br />Di Utente Pusk - opera propria,
                    <a
                      href="//it.wikipedia.org/wiki/File:Snampanigaglia.jpg"
                      title="Public domain"
                      >Pubblico dominio</a
                    >,
                    <a href="https://it.wikipedia.org/w/index.php?curid=1254138"
                      >Collegamento</a
                    >
                  </p>
                </div>
              </b-col>
            </b-row>

            <div style="margin-left: auto; margin-right: auto">
              <iframe
                title="Rigassificatori in Italia"
                aria-label="Locator maps"
                id="datawrapper-chart-4Rjc3"
                src="https://datawrapper.dwcdn.net/4Rjc3/7/?dark=true"
                scrolling="no"
                frameborder="0"
                style="
                  width: 0;
                  min-width: 100% !important;
                  border: none;
                  color: white;
                "
                height="950"
              ></iframe>
            </div>
            <div id="body_section_center">
              In una situazione di emergenza come quella che si prospetta per
              l’inverno 2022, Il GNL sembra essere il rimedio più immediato per
              mettere in sicurezza il paese. Attraverso le navi ci si può
              rifornire potenzialmente da tutto il mondo e se si creano
              conflitti con il produttore preferito, semplicemente esso può
              essere sostituito con un altro.
            </div>
          </b-container>

          <b-container>
            <div class="section" id="fourth_section" data-aos="fade-up">
              <h1 class="title_section" style="text-align:center">
                Ma perchè tanta ostruzione al rigassificatore di Piombino?
              </h1>
              <div class="body_section body_section_center">
                I timori della popolazione sono soprattutto sulle potenziali
                conseguenze ambientali che potrebbe avere. Piombino, non è una
                città qualsiasi, per capire le radici delle proteste è
                importante conoscere anche la storia ambientale di questa città.
                <br />É il 2012, le acciaierie Lucchini chiedono
                l'amministrazione straordinaria: è la fine di una storia che era
                iniziata nel 1865 con la creazione delle
                <a
                  href="https://st.ilsole24ore.com/art/economia/2011-08-03/piombino-guarda-oltre-acciaio-064037_PRN.shtml"
                  >officine Perseveranza</a
                >. Per molti anni la città di Piombino ha deciso di convivere
                con questa forma di ricchezza. Per pane e lavoro si era scelto
                di accettare anche quello “spolverino” che ogni mattina sporcava
                i vetri delle finestre. <br />Ma da quando nel 2014 tutto il
                sistema industriale è collassato, la riconversione e
                l’ammodernamento è diventato un miraggio. Tutto ciò che è
                rimasto sono solo 928,4 ettari di terra e 2015 ettari di mare da
                bonificare, quello che i più conoscono come zona di interesse
                nazionale (<a
                  href="https://www.affarItaliani.it/cronache/piombino-tra-inquinamento-rifiuti-e-rischi-per-la-salute-serve-bonificare-757523.html?refresh_ce"
                  >SIN</a
                >). Oltre al decadente impianto siderurgico, nella zona
                periferica della città sorge anche una grossa discarica che i
                residenti chiamano affettuosamente
                <a
                  href="http://www.corriereetrusco.it/2018/02/24/piombino-a-colmata-riunione-per-i-miasmi-di-monte-puzzo/"
                  >“Monte Puzzo”</a
                >.
              </div>
              <div class="plot_container">
                <b-img
                  src="static/imgs/montepuzzo.png"
                  style="width: 35rem"
                ></b-img>
              </div>
              <div class="body_section body_section_center">
                Sembra esserci anche un'altra gravosa eredità per i cittadini di
                Piombino che i vari comitati di salute pubblica della zona già
                da tempo sospettavano, ovvero che gli impianti industriali
                abbiano provocato delle conseguenze sulla salute dei cittadini.
                Sospetti confermati da una recente ricerca svolta dalla “Società
                della Salute Valli Etrusche” che ha mostrato un
                <a
                  href="https://www.quinewsvaldicornia.it/piombino-commissione-salute-chiesto-biomonitoraggio.htm"
                  >aumento del 51% delle malformazioni congenite nei neonati</a
                >. A questo proposito è stato di recente inoltre avviato un
                biomonitoraggio per approfondire il tema e tutto ciò mostra la
                forte criticità che sta vivendo la città e la zona limitrofa di
                Piombino. <br />La cittadinanza si sente abbandonata e
                sfruttata, le sono stati chiesti molti sforzi per il bene
                dell'Italia eppure sembra non ricevere molto in cambio,
                addirittura la sanità territoriale diminuisce sempre di più: è
                da tempo in atto il ridimensionamento dell’ospedale, infatti il
                punto nascite di Villamarina è già stato chiuso. <br /><br />
                Il rigassificatore andrebbe a inserirsi in un'area già
                abbastanza inquinata e che ogni giorno è continuamente solcata
                dal traffico marino. In teoria Il golfo di Piombino fa parte del
                <a
                  href="https://www.infoelba.it/isola-d-elba/parco-nazionale-arcipelago-toscano/santuario-cetacei/"
                  >santuario dei Cetacei </a
                >, ma è una definizione molto superficiale perché indica un
                luogo di avvistamento e niente più: non c’è nessuna protezione
                effettiva per questi mammiferi. L’intenso traffico marittimo è
                già un problema per la loro sopravvivenza.
                <br />
                Secondo uno studio di Panigada nel mare attorno alla corsica, si
                verificano all’incirca l’82% di tutte le collisioni tra cetacei
                e navi del mediterraneo occidentale (vedi
                <a href="https://pubmed.ncbi.nlm.nih.gov/16712877/"
                  >Whale strikes</a
                >).
              </div>
              <div id="plot_container">
                <b-img src="./static/imgs/panigandaimg.png"></b-img>
              </div>
              <div id="body_section_center">
                Per finire non mancano nemmeno le microplastiche. Al largo di
                Piombino, secondo uno studio francese, c’è una forte
                concentrazione di microplastiche nel mare, di circa 88 215
                MPs/km2 . Il pesce catturato dai pescatori piombinesi dunque è
                già in parte compromesso e questo senza ancora il
                rigassificatore.
                <br />
                Sono molte le altre le ricerche come quelle di Legambiente e
                Expedition MED che si stanno preoccupando del problema della
                micro-plastica. Il Portale LitterBase mostra i principali studi
                sulle microplastiche nel mediterraneo, si può notare come
                <a
                  href='https://maps.awi.de/awimaps/projects/public/?cu=Litter_and_microplastic_distribution&sb=e&zm=10&ctr=[42.72104240884391,10.0083670578897]&lyr=["fram-litter_and_microplastic_distribution::items_/_km/:sup2;_/:makeunique_l_0;34019"]&fltr={"time":["1960-01-01T00:00:00","2022-12-31T23:59:59"],"attr":{"0":[0,0,0],"1":[0,0,0,0,0]}}#home'
                  >tutti gli studi effettuati al largo di Piombino</a
                >
                mostrino una alta concentrazione di microplastiche nel mare Per
                Approfondire ogni singola analisi basta cliccare sui Pallini che
                appaiono nella cartina interattiva e si verrà rimandati allo
                studio specifico.
                <br />
                Inoltre Legambiente monitora i mari anche con un progetto
                chiamato “goletta verde”, in
                <a href="https://golettaverde.legambiente.it/mappa-monitoraggi/"
                  >questa cartina interattiva</a
                >
                , si puo' notare come nella zona portuale le acque reflue non
                sono trattate adeguatamente; La situazione e´ anche peggio se
                pensiamo che i dati di ARPAT sulla SIN purtoppo non sono
                disponibili al pubblico. Per trovare una bandiera blu bisogna
                allontanarsi fino alla riserva naturale della Sterpaia.
                <br />
                Purtroppo il mare che circonda Piombino presenta già delle
                criticità, tuttavia la città ha deciso di puntare sul turismo,
                sulla bellezza della natura e delle sue acque che però non
                vengono rispettate.
                <div id="plot_section">
                  <iframe title="Qualità delle acque vicino a Piombino" aria-label="Locator maps" id="datawrapper-chart-0BUCn" src="https://datawrapper.dwcdn.net/0BUCn/4/?dark=true" scrolling="no" frameborder="0" style="width: 0; min-width: 100% !important; border: none;" height="1020"></iframe>
                </div>
                <div id="body_section_center">
                  Gli abitanti e i pescatori non sembrano molto coscienti di
                  questa situazione: protestano pensando a un possibile
                  inquinamento dei loro mari quando la realtà è ben diversa da
                  quello che le immagini da cartolina fanno pensare. L’imminente
                  arrivo della FSRU ha innescato nei cittadini e nei pescatori
                  paure e incertezze sul loro futuro.
                  <br />
                  C’è da far notare che mancano studi che possano affermare
                  quale sarà l'esatto impatto ambientale del cloro usato nel
                  processo di rigassificazione. A questa paura basterebbe
                  rispondere attraverso lo studio sull’impatto ambientale del
                  rigassificatore in modo da essere trasparenti e non alimentare
                  dubbi e sospetti nei piombinesi, come pare stiano facendo le
                  forze politiche in questo momento.
                </div>
              </div>
            </div>
          </b-container>
          <br />
          <b-container>
            <div class="section" id="fifth_section" data-aos="fade-up">
              <h1 class="title_section">
                La politica nazionale e la propaganda
              </h1>
              <div class="plot_section">
                <PolCarousel></PolCarousel>
              </div>
              <div class="body_section_center">
                Le proteste a Piombino nel mese di maggio sono state numerose e
                molto partecipate dalla cittadinanza. Piombino è spesso al
                centro dei notiziari, infatti è diventato un tema molto popolare
                nello scontro politico tra partiti locali e nazionali.
                <br />Le paure e la confusione dell’opinione pubblica arrivano
                anche dal comportamento opportunistico dei singoli politici e da
                una marcata linea da parte dei loro partiti. <br />In Italia non
                basta, come negli Stati uniti, associare alla destra la figura
                di partito che supporta il fronte anti-ambientalista. Nel caso
                di Piombino, il sindaco Francesco Ferrari, contrario al
                rigassificatore, appartiene al Partito Fratelli d’Italia: un
                partito che ha sempre deriso i movimenti ambientalisti come
                <a href="https://www.youtube.com/watch?v=Xn_GxaCE-9c"
                  >Friday For Future</a
                >. <br />Negli anni passati, come riferito da Legambiente, lo
                stesso sindaco ha bocciato progetti di Agro Fotovoltaico nella
                città affermando che avrebbero rovinato una presunta vocazione
                turistica della
                <a
                  href="https://greenreport.it/news/economia-ecologica/piombino-dice-no-al-fotovoltaico-ma-apre-allipotesi-rigassificatore/"
                  >Città</a
                >. Il partito Fratelli d’Italia molte volte è andato contro
                quella che definisce "Ideologia Ecologista”, accusando gli
                ambientalisti di bloccare lo sviluppo economico. Riportiamo di
                seguito le parole della Meloni:
                <blockquote>
                  <a
                    href="https://greenreport.it/leditoriale/il-manifesto-di-giorgia-meloni-e-dei-fratelli-dItalia-contro-la-transizione-verde/"
                  >
                    «[...] alla cieca ideologia green che tutto blocca e tutto
                    impedisce. »
                  </a>
                </blockquote>
                Esattamente quello fatto dal sindaco Francesco Ferrari con gli
                impianti agrofotovoltaici. La contraddizione più grande di tutta
                la politica è quella legata al voto sulla tassonomia verde. Per
                tassonomia verde intendiamo un documento dell'Unione Europea che
                definisce su quali settori bisogna puntare per poter ridurre i
                cambiamenti climatici. Il 7 luglio del 2022 con un
                <a
                  href="https://www.ilfattoquotidiano.it/2022/07/06/tassonomia-verde-il-parlamento-ue-dice-si-allinserimento-di-gas-e-nucleare-con-i-voti-del-centrodestra-Italiano-pd-e-m5s-per-il-rigetto/6651771/"
                  >voto finale</a
                >
                sono stati inclusi nella tassonomia verde anche il gas e il
                nucleare. Tra i partiti Italiani a favore c’erano Fratelli
                d'Italia, Lega e Forza Italia mentre, tra chi ha votato contro,
                si osservano il PD e M5S. In Europa quindi, PD e M5S hanno
                votato contro gas e nucleare, mentre a favore sono stati FI,
                FDI,LEGA e IV. Invece, quando si è trattato di votare per il
                rigassificatore nel parlamento Italiano solo FDI è andata contro
                il decreto.
              </div>
              <div class="plot_section">
                <b-img
                  src="./static/imgs/Rigassifica.png"
                  style="width: 30rem"
                ></b-img>
              </div>
              <div class="body_section_center">
                Dato curioso: al PD appartiene anche il commissario per l’opera
                (il governatore della Toscana Giani) e un altro sostenitore del
                progetto a livello regionale ( il sindaco di Firenze Dario
                Nardella) che
                <a
                  href="https://www.iltirreno.it/toscana/2022/07/05/news/il-rigassificatore-e-come-bilancino-tanti-non-lo-volevano-ma-ci-ha-salvati-1.100044274"
                  >paragona</a
                >
                il rigassificatore al bacino idrico del Bilancino e accusa il
                sindaco di Piombino di alimentare ingiustificati movimenti
                NIMBY. <br />Tutto ciò fa pensare che a livello locale, anche i
                partiti meno ambientalisti giochino questa carta per evitare di
                perdere elettori e anche la sinistra ecologista si trova in una
                situazione di imbarazzo: a livello locale contro il
                rigassificatore, a livello nazionale e regionale a favore e,
                infine, a livello europeo rifiutano il gas nella tassonomia
                Verde. <br />Lo spirito della città è contrario, L’industria che
                un tempo dava lavoro a tutta la città oggi è solo un cumulo di
                rifiuti in attesa di bonifica e in molti hanno la sensazione di
                essere abbandonati dallo Stato. Con il senso di abbandono
                creato, è naturale che le comunità (quindi i partiti locali)
                comincino ad avere paure anche infondate: paura per il
                rigassificatore, paure per la contaminazione del mare e dei
                pesci; paure sulla svalutazione degli immobili e infine il
                timore che tragedie come quelle di Livorno, Beirut e
                <a
                  href="https://www.forbes.com/sites/christopherhelman/2022/06/08/fire-knocks-out-billionaire-owned-natural-gas-export-plant-in-texas-nations-second-biggest/?sh=6898ee8f6bf6"
                  >Galveston</a
                >
                possano
                <a
                  href="https://www.open.online/2022/06/30/toscana-m5s-silvia-noferi-rigassificatore-piombino-missile-putin/"
                  >ripetersi</a
                >. <br />A dover di cronaca, ci sono anche associazioni come
                Legambiente sono contrarie al rigassificatore anche perchè il
                GNL secondo loro non è una fonte verde, e non risolve il vero
                problema dell’Italia: la dipendenza energetica da combustibili
                fossili stranieri. <br /><br />
                Ripetiamo che quello che secondo noi manca è una buona
                comunicazione verso i cittadini che riesca a contrastare le
                paure che la propaganda politica ha fatto affiorare. La paura
                dei piombinesi è giustificata perché nasce dall’incertezza e da
                mancanze di risposte. Inoltre, la soluzione temporanea (i.e.
                aumento dell’uso di gas e dunque dei rigassificatori ) rischia
                di protrarsi fino al 2050 per via della recente tassonomia
                verde. Nel frattempo i 3 anni dell’
                <a
                  href="https://www.ilpost.it/2022/07/14/accordo-rigassificatore-piombino/"
                  >accordo</a
                >
                appena raggiunto potrebbero aumentare.
              </div>
            </div>
          </b-container>

          <b-container>
            <div class="section" id="sixth_section" data-aos="fade-up">
              <h1 class="title_section">
                Perchè abbandonare fonti fossili: indipendenza energetica e
                salvaguardia ambiente
              </h1>
              <div class="body_section_center">
                La criticità è che in questo modo si diventa sempre dipendenti
                dall’estero.
                <br />Certamente il gas è una ottima fonte fossile ponte, in
                poco tempo si possono ridurre notevolmente le emissioni di gas
                serra, il problema è quando una energia ponte diventa definitiva
                come sembra essere per l’Italia che in questi anni non ha mai
                cercato di rendersi meno dipendente. <br />La crisi del gas
                russo è simile alla crisi che l'Italia si è trovata ad
                affrontare nel 2010 a seguito dei cambi di regime nel nord
                africa, le famose primavere arabe.
              </div>
              <div class="plot_section">
                <LineImports></LineImports>
              </div>

              <div class="body_section_center">
                <br />Se analizziamo la seguente serie possiamo notare come nel
                2010 l’Italia abbia dovuto sostituire i suoi fornitori nord
                africani con quelli russi. Vediamo che Libia e Algeria crollano
                rapidamente e il loro gas viene sostituito gradualmente da
                quello russo. All’epoca, le primavere arabe avevano rovesciato
                le dittature nordafricane creando un vuoto di potere e guerre
                civili che continuano tuttora. Nuovamente, la linea del governo
                è stata quella di ritornare ad approvvigionarsi da questi paesi,
                in particolare l’<a
                  href="https://www.ilsole24ore.com/art/gas-dall-algeria-4-miliardi-metri-cubi-piu-all-Italia-AE6TtrmB"
                  >Algeria</a
                >. <br />Secondo noi il gas e le fonti fossili ci lasciano in
                balìa della geopolitica, creando instabilità, diminuendo la
                crescita e aumentando il costo della vita. <br />La priorità
                dovrebbe essere l’indipendenza energetica, cosa che di certo non
                può avvenire dall’oggi al domani, ma va programmata sul lungo
                termine. In un paese come l’Italia, che come visto non ha grosse
                risorse fossili, l’unica soluzione sono le rinnovabili o il
                nucleare, però in Italia quest’ultimo è stato bocciato da 2
                referendum (1987, 2010). <br />Passare alle rinnovabili non è
                solo un tema di indipendenza nazionale, è l’unica maniera di
                fermare il cambiamento climatico, occorre azzerare le emissioni
                di gas serra nei prossimi anni. <br />L’unione europea sta
                cercando di diventare un modello per il mondo e si è imposta di
                raggiungere la
                <a
                  href="https://ec.europa.eu/info/strategy/priorities-2019-2024/european-green-deal_it"
                  >Carbon Neutral entro il 2050</a
                >.
                <div class="plot_section">
                         <LineEurostat></LineEurostat>

                </div>
                <div class="body_section_center">
                  Questo è l’obiettivo a lungo termine. Già per il 2030 (tra 8
                  anni) l’Italia si è imposta di ridurre le sue emissioni del
                  55% (rispetto al 1990). Una sfida ambiziosa, ma forse anche
                  troppo. Questo grafico ci mostra come siano diminuite le
                  emissioni rispetto al 1990. In 22 anni sono diminuite solo del
                  (Inserire Percentuale). L'obiettivo è raggiungibile in soli 8
                  anni? Il ministro Cingolani è ottimista e ha deciso di puntare
                  al gas come energia “ponte” per sostituire prima di tutto
                  altri combustibili fossili, come il carbone e il petrolio che
                  tuttavia hanno un impatto più alto per l’effetto serra. Il
                  progetto Italiano, seguendo il piano di sviluppo di Terna del
                  2021, prevede che per il 2030 siano installati impianti
                  rinnovabili per una produzione di 70 GWh. La criticità, come
                  visto dai dati Terna, è che dal 2013 in poi la crescita di
                  nuovi kwh da rinnovabile è scesa. Se tra il 2008 e il 2013 i
                  nuovi impianti sono aumentati di circa 4,6 GW/anno, tra il
                  2013 e il 2020 la crescita è stata solo dello 0.8 GW/anno. La
                  novità è che intanto le richieste di allaccio a Terna in
                  questi stessi anni (2013-2020) sono aumentate in maniera molto
                  più consistente, questo vuol dire che da parte dei privati ci
                  sono buone intenzioni sull’installazione di nuovi impianti ma
                  le proposte si scontrano con la realtà.
                </div>
              </div>
            </div>
          </b-container>
          <b-container>
            <div class="section" id="seventh_section" data-aos="fade-up">
              <h1 class="title_section">
                Perché siamo indietro con le rinnovabili
              </h1>
              <div class="body_section_center">
                Infatti sono molti i problemi che ostacolano in Italia la
                creazione di nuove infrastrutture per le energie rinnovabili.
                Come mostrato nel
                <a
                  href="https://www.legambiente.it/wp-content/uploads/2021/11/Scacco-matto-alle-rinnovabili_report-2022.pdf"
                  >report di Legambiente</a
                >, i motivi sono tanti e cambiano da ogni singolo caso, ma il
                filo in comune di tutti i casi esposti è la burocrazia.
              </div>

              <div class="plot_section">
                <b-img src="./static/imgs/roadmap.png" style="width: 30rem;"></b-img>
              </div>

              <div class="body_section_center">
                Ad oggi, secondo il ministro Cingolani sarà più facile ottenere
                i permessi per le rinnovabili, promette che se prima si
                impiegavano 6 anni ora basteranno solo
                <a
                  href="https://www.ansa.it/europa/notizie/rubriche/altrenews/2022/06/27/cingolani-avanti-su-decarbonizzazione-ue-trasporti_01a68fe5-3bfd-4445-9358-e2c3ee9080d0.html"
                  >300 giorni</a
                >. <br />
                Ma si potrà in 8 anni raddoppiare la produzione? <br />Per
                rispettare la scaletta del Green Deal dobbiamo arrivare a una
                produzione di 70 gw/anno e ad ora siamo a poco più di 30
                gw/anno. Per lo meno Terna pone il 2030 come data per avere
                ultimata una rete smart capace di gestire l’instabilità data
                dalle rinnovabili. Per il 2050 invece, per eseguire i piani
                europei, la produzione di rinnovabili dovrà essere addirittura
                di 200 gw/anno.
              </div>

              <div class="body_section_center">
                Un piano energetico che punti a rispettare gli accordi del clima
                l’Italia sembra avercelo, la nostra opinione è che il problema
                sia l’attuazione di questo piano molto ambizioso. Si tratta di
                dare un significativo cambio di rotta rispetto agli ultimi anni
                in termini di riduzione di gas serra e aumento delle
                rinnovabili. Le preoccupazioni sono dovute soprattutto a causa
                degli eventi straordinari e imprevedibili che stanno già nel
                primo semestre del 2022 compromettendo la buona riuscita del
                piano già ai suoi inizi.
                <br />Ad esempio l’inflazione odierna che erode silenziosamente
                i fondi del PNRR Italiano; in secondo luogo abbiamo l’ aumento
                delle materie prime per l’efficientamento energetico come quelle
                per la produzione di pannelli solari e turbine eoliche. Se il
                preventivo crescerà troppo, molti privati rinunceranno a
                costruire progetti che fino a pochi anni fa erano sostenibili. A
                questo si aggiungono anche guerre e epidemie che potrebbero dare
                un freno ulteriore a progetti che hanno bisogno di un sostegno
                continuo e a lungo termine. L’ottimismo sulla riuscita del piano
                del ministro Cingolani, non è condiviso da molte associazioni
                ambientaliste: sospetto è che, vista la difficoltà di non
                convertirsi al rinnovabile in così poco tempo, in Europa si
                punti al nucleare che è appena stato inserito nella tassonomia
                verde insieme al gas. <br /><br />
                Il gas è qualcosa di temporaneo, per il futuro, se si vogliono
                abbassare le emissioni entro il 2050, le rinnovabili devono
                aumentare.
                <br /><br />
                Altra critica che viene mossa al governo è che in caso di
                emergenza energetica nazionale, venga subito nominato un
                commissario speciale che possa accelerare i tempi di
                realizzazione e superare tutti gli ostacoli del caso (le
                comunità locali spaventate e la burocrazia) nella realizzazione
                di impianti di rigassificazione. Ma perchè per le rinnovabili
                non vale lo stesso?
                <br />Secondo un'intervista a report del ministro Cingolari:
                <blockquote>
                  <i
                    >“Per fare queste cose non serve un commissario, per fare
                    queste cose serve che lo stato funzioni con le
                    autorizzazioni veloci e che gli imprenditori facciano i loro
                    investimenti, il commissario si fa in caso di emergenza”
                    “Non è una emergenza questa con la crisi ucraina?”, “I
                    produttori di rinnovabili che vengono a chiedere un
                    commissario per installare le rinnovabili non sono un’
                    emergenza”.</i
                  >
                </blockquote>
                Eppure in Italia, sono molti i casi di impianti rinnovabili che
                sono in attesa di approvazione. Anche in questi casi, come con
                il rigassificatore, i progetti vengono ostacolati da movimenti
                NIMBY e da amministratori locali che pensano che le rinnovabili
                possano creare danni all’ambiente. La ragion di stato e il
                primato della scienza sembrano valere solo per le energie
                fossili in Italia, ma non per le rinnovabili.
                <br />Certo, gli iter e le procedure sono importanti per creare
                impianti di qualità ed evitare la speculazione che è presente
                nel mondo delle rinnovabili. Ad esempio il maggior latitante
                Italiano Matteo Messina Denaro, nel 2017 secondo gli inquirenti
                stava investendo in parchi eolici siciliani. Come raccontato dal
                <a
                  href="https://web.facebook.com/dataroom.milena.gabanelli/videos/energie-rinnovabili-perch%C3%A9-lItalia-%C3%A8-cos%C3%AC-indietro-tutti-gli-ostacoli-agli-impia/3220569398267321/?_rdc=1&_rdr"
                  >Presidente Della Regione Puglia</a
                >, in molti chiedono autorizzazioni solo per poi rivenderle a
                multinazionali che poi cercheranno di attuare i progetti non
                efficienti e validi. Anche gli incentivi pubblici fanno gola
                agli speculatori e alle mafie, ma questi eventi negativi non
                possono prevalere sulla bontà tecnologica delle rinnovabili.
                <br />Per capire meglio tutti gli ostacoli che incontrano le
                rinnovabili secondo il report del 2021, realizzato da
                Legambiente, in Italia le richieste di allaccio inviate a Terna
                sono state numerose ma i progetti realizzati sono stati molto
                meno, l’energia da rinnovabili è aumentata solo dello 0.8%.
              </div>
              <div class="plot_section">
                <iframe
                  width="600"
                  height="371"
                  seamless
                  frameborder="0"
                  scrolling="no"
                  src="https://docs.google.com/spreadsheets/d/e/2PACX-1vRWiqUD8cW-IhRp2Sx5tocJgKuJf4JJ7ZTUmjHSjdSIUG9yDX0e7YZ-UA3QSS1SelryWAhRQhP7pzyH/pubchart?oid=103999947&amp;format=interactive"
                ></iframe>
              </div>
              <br />
              <div class="body_section_center">
                Dopo il boom del 2013 l’Italia ha visto crescere molto
                lentamente il numero di impianti rinnovabili.
              </div>

              <div class="plot_section">
                <RenewableD3></RenewableD3>
              </div>

              <div class="body_section_center">
                Infatti qualsiasi impianto deve passare attraverso un complicato
                iter amministrativo e in ogni singolo passo potenzialmente
                potrebbe esserci un ente che ha potere di veto, anche un singolo
                veto potrebbe rendere nulla tutta la catena autorizzativa.
                <br />Molto spesso questi progetti vengono bloccati da parte di
                vari amministratori locali che hanno paura di scontentare
                l’elettorato locale e sotto l’onda emotiva generale vengono
                bloccati. Sono ancora molti i pregiudizi sulle rinnovabili:
                deturpano i paesaggi, uccidono gli uccelli, tolgono spazio
                all’agricoltura e alla pastorizia, creano un rumore
                insopportabile. <br />Certamente non ogni luogo è ideale per
                installare pale eoliche, ed è un bene che ci siano protezioni e
                tutele ambientali e del paesaggio. Purtroppo solo recentemente
                si è creato un approccio sensato: far individuare direttamente
                alle regioni e in anticipo i terreni di importanza ambientale e
                culturale. Non potranno quindi essere creati vincoli a
                posteriori, che permettano a movimenti NIMBY di bloccare le
                opere.
              </div>
              <div class="plot_section">
                <b-img src="./static/imgs/why.png" style="width: 30rem"></b-img>
              </div>

              <div class="body_section_center">
                Come visto, come per il gas e per le rinnovabili, i pregiudizi,
                la propaganda e le favole che si ascoltano al bar vanno a creare
                problemi a carattere nazionale. Il tema anche qua dipende da una
                buona cultura della popolazione sui temi energetici e
                scientifici in generale, servirebbe più divulgazione per
                sensibilizzazione e tranquillizzare su questi temi, togliendo
                ogni forma di propaganda e ideologia energetica. L’Italia e gli
                Italiani dovrebbero decidere per davvero che energia vogliano
                per il loro futuro, e che se non vogliono usare giacimenti
                esteri bisogna sacrificarsi un po' almeno finché la
                <a
                  href="https://www.enea.it/it/Ricerca_sviluppo/nucleare/fusione-nucleare"
                  >fusione nucleare</a
                >
                non diventi realtà. Piombino è un simbolo anche di questa
                ipocrisia: una città che
                <a
                  href="https://www.comune.piombino.li.it/area_letturaNotizia/66541/pagsistema.html"
                  >rifiuta energie verdi</a
                >
                ma che rifiuta anche quelle fossili. Sarebbe bello sapere
                esattamente come hanno intenzione di caricare i loro telefoni i
                piombinesi nel futuro.
              </div>
            </div>
          </b-container>
        </section>
      </main>
    </div>
  </span>
</template>

<style>
hr {
  background-color: white;
}

.section {
  margin-bottom: 2rem;
  margin-top: 2rem;
  background-color: #27282e;
}

.plot_section,.plot_container, #plot_container{
    text-align: center;

}

.body_section_center {
  width: 75%;
  margin-left: auto;
  margin-right: auto;

    text-justify: center;

}

.cols_var {
  background-color: #27282e;
}
.title_section_left {
  text-align: left;
}
#menu_toggle {
  position: fixed;
  margin-top: 4rem;

  z-index: 999;
}
.idx_els {
  background-color: transparent !important;
  border-color: transparent !important;
  color: black !important;
}

.idx_els:hover {
  text-decoration: underline !important;
}

#index {
  position: fixed;
  z-index: 998;
  max-height: 150vh;
  overflow-y: hidden;

  padding-top: 1rem;
}
#article_body {
  top: 6rem;
  margin-left: auto;
  margin-right: auto;
}

p > .export-text {
  color: white !important;
}

#gotomap_btn {
  background: transparent;
  border: transparent;
  margin-right: -10px;
  margin-left: -10px;
  margin-bottom: 5px;
  color: #007bff;
}

#gotomap_btn:hover {
  color: #0056b3;
  border: transparent;
  text-decoration: underline;
}
#idx_els {
  font-size: 5px;
}



@media screen and (max-width: 1300px) and (min-width: 900px) {
  #article_body {
    position: sticky;
    margin-top: 6rem;
    margin-left: 12rem;
  }
  #menu_toggle {
    font-size: 15px;
    margin-top: 0rem;
    opacity: 0.8;
  }
}

@media screen and (max-width: 900px) and (min-width: 1px) {
  #article_body {
    position: sticky;
    margin-left: auto;
    margin-right: auto;
    margin-top: 6rem;
  }
  #navbar_ {
    position: fixed;
    z-index: 1000;
  }

  #plot_section {
    margin-left: auto;
    margin-right: auto;
    width: 50rem;
  }
  #menu_toggle {
    font-size: 10px;
    margin-top: 0rem;
    opacity: 0.8;
  }
}
</style>
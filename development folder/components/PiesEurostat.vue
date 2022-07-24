<script>
import * as d3 from "https://cdn.skypack.dev/d3@7";

//CHANGE SIZE OF WINDOW (to make aos work on first load; now it works only if F12 is pressed)
export default {
  name: "PiesEurostat",
  props: {},
  data() {
    return {
        eurostat_:{}
    };
  },
   mounted(){
    Promise.all([d3.csv('./static/mix_energetico_eurostat.csv')]).then((csvs)=>{
        this.eurostat_=csvs[0]
    })
   }
  ,
  methods: {
    pie_mix_eurostat() {
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

      Plotly.newPlot("pies_eurostat", data_, layout);
    },
  },
  watch: {
    eurostat_:function(newVal){
        this.pie_mix_eurostat()
    }
  },
};
</script>

<template>
  <div id="pies_eurostat"></div>
</template>
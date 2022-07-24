<script>
import Vue from "vue";
import * as d3 from "https://cdn.skypack.dev/d3@7";

export default {
  name: "MiseProdPlot",
  data() {
    return {
      mise_prod_data: {},
    };
  },
  mounted() {
    Promise.all([d3.csv("./static/produzione_ALL_PULITO.csv")]).then((csvs) => {
      //MISE
      //produzione idrocarburi
      this.mise_prod_data = csvs[0];
    });
  },
  methods: {
    barchart(out, data_) {
      var temp_values = [];
      var temp_places = [...new Set(data_.map((d) => d.Regione))].sort();
      var temp_minerals = [...new Set(data_.map((d) => d.Minerale))].sort();

      for (let i = 0; i < temp_places.length; i++) {
        var temp_l = [];
        var filt_region = data_.filter((d) => d.Regione == temp_places[i]);
        for (let j = 0; j < temp_minerals.length; j++) {
          var temp_data = filt_region.filter(
            (d) => d.Minerale == temp_minerals[j]
          );
          var s = 0;
          var temp_values_ = temp_data.map(
            (d) => (s += Math.log(d.Produzione))
          );
          temp_l.push(s);
        }
        temp_values.push(temp_l);
      }

      var data = [];
      for (let i = 0; i < temp_minerals.length; i++) {
        data.push({
          y: temp_places,
          x: temp_values.map((d) => d[i]),
          name: temp_minerals[i],
          orientation: "h",
          type: "bar",
        });
      }
      var layout = {
        margin: { t: 0, l: 0 },
        barmode: "stack",
        width: 200,
        height: 500,
        xaxis: { name: "Log scale - k" },
        yaxis: { showticklabels: false },
               hoverlabel: {
          bgcolor: "rgba(0,0,0,0.8)",
          font: { color: "white" },
        },
        paper_bgcolor: "rgba(0,0,0,0)",
        plot_bgcolor: "rgba(0,0,0,0)",
        font: {
          //family: 'Courier New, monospace',
          //size: 18,
          color: "white",
        },
      };
      Plotly.newPlot(out, data, layout, { displayModeBar: false });
    },
    linechart(out, data_) {
      var temp_values = [];
      var temp_dates = [...new Set(data_.map((d) => d.Anno))].sort();
      temp_dates = temp_dates.splice(0, 43);
      var temp_minerals = [...new Set(data_.map((d) => d.Minerale))].sort();

      for (let j = 0; j < temp_minerals.length; j++) {
        var temp_mineral_l = [];
        var filt_mineral = data_.filter((d) => d.Minerale == temp_minerals[j]);
        for (let i = 0; i < temp_dates.length; i++) {
          var temp_data = filt_mineral.filter((d) => d.Anno == temp_dates[i]);
          var s = 0;
          var temp_values_ = temp_data.map(
            (d) => (s += Math.log(d.Produzione))
          );
          temp_mineral_l.push(s);
        }
        temp_values.push(temp_mineral_l);
      }
      var data = [];

      for (let i = 0; i < temp_minerals.length; i++) {
        data.push({
          x: temp_dates,
          y: temp_values[i],
          name: temp_minerals[i],
          type: "scatter",
        });
      }
      var layout = {
        margin: {
          t: 0,
          b: 0,
          l: 170,
        },
        width: 879,
        height: 210,
        yaxis: {
          title: "Log scale - kg",
        },
        xaxis: {
          showticklabels: false,
        },
                hoverlabel: {
          bgcolor: "rgba(0,0,0,0.8)",
          font: { color: "white" },
        },
        paper_bgcolor: "rgba(0,0,0,0)",
        plot_bgcolor: "rgba(0,0,0,0)",
        font: {
          //family: 'Courier New, monospace',
          //size: 18,
          color: "white",
        },
      };

      return Plotly.newPlot(out, data, layout, { displayModeBar: false });
    },
    bar2d(y, x, z) {
      var data = [
        {
          y: y.map(String),
          x: x.map(String),
          z: z.map(d=>Math.sqrt(d)),
          histfunc: "sum",
          colorscale: "Hot",
          type: "histogram2d",
          reversescale:true,
          showlegend:true,
          colorbar: {
            x: -0.25,
            ticklabelposition: "inside",
            tickfont: { color: "green", size: 15, family: "Droid Sans Mono" },
          },
        },
      ];
      var layout = {
        margin: {
          t: 0,
        },
        width: 910,
        height: 500,

        xaxis: {
          categoryorder: "category ascending",
        },
        yaxis: {
          categoryorder: "category ascending",
        },
                hoverlabel: {
          bgcolor: "rgba(0,0,0,0.8)",
          font: { color: "white" },
        },
        paper_bgcolor: "rgba(0,0,0,0)",
        plot_bgcolor: "rgba(0,0,0,0)",
        font: {
          //family: 'Courier New, monospace',
          //size: 18,
          color: "white",
        },
      };
      Plotly.newPlot("historical_prod_heat", data, layout, {
        displayModeBar: false, legend:true
      });
    },
  },
  watch: {
    mise_prod_data: function (newVal) {
      //console.log(this.prod_regions);
      this.linechart("historical_prod_marginalx", this.mise_prod_data);
      this.barchart("historical_prod_marginaly", this.mise_prod_data);
      this.bar2d(
        this.mise_prod_data.map((d) => d.Regione),
        this.mise_prod_data.map((d) => d.Anno),

        this.mise_prod_data.map((d) => d.Produzione)
      );
    },
  },
};
</script>

<template>
  <b-container id="heat_prod">
    <div id="historical_prod_marginalx"></div>
    <b-row>
      <b-col cols="9"> <div id="historical_prod_heat"></div></b-col>
      <b-col><div id="historical_prod_marginaly"></div></b-col>
    </b-row>
  </b-container>
</template>

<style>
#heat_prod{
  margin-left:5rem;
  margin-top:5rem
}
#historical_prod_marignaly{
  margin-left:5rem;
}
</style>
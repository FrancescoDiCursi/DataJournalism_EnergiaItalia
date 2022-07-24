<script>
import * as d3 from "https://cdn.skypack.dev/d3@7";
export default {
  name: "LineImport",
  props: {},
  data() {
    return {
      imports: {},
    };
  },
  mounted() {
    Promise.all([d3.csv("./static/imports-natural-gas.csv")]).then((csvs) => {
      this.imports = csvs[0];
    });
  },
  methods: {
    imports_linechart() {
      var names = this.imports.map((d) => d.Label);

      var traces_ = [];
      for (let i = 0; i < names.length; i++) {
        var yrs = Object.keys(this.imports[i])
          .slice(0, -1)
          .map((d) => +d);
        var values = Object.values(this.imports[i])
          .slice(0, -1)
          .map((d) => +d);


        var temp_trace = {
          x: yrs,
          y: values,
          name: names[i],
          mode: "lines+markers",
        };

        traces_.push(temp_trace);
      }

      var layout = {
        title:
          "Importazioni gas naturale (in terajoul, EUROSTAT)",
        hovermode: "x unified",
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
        xaxis: {
          autorange: true,
          rangeselector: {
            buttons: [
              {
                count: 1,
                label: "1 mese",
                step: "month",
                stepmode: "backward",
              },
              {
                count: 6,
                label: "6 mesi",
                step: "month",
                stepmode: "backward",
              },
              { label:'Tutto',
                 step: "all" },
            ],
          },
          rangeslider: { },
          type: "year", //not working but date it's not significant (only years avaiable), by putting a wrong value, the button doesn't appear but there's no problem with x axis zoom
        },
        shapes: [

    //line vertical

    {
      type: 'line',
      x0: 2010,
      y0: 0,
      x1: 2010,
      y1: 1500000,
      line: {
        color: 'rgba(227,26,28,0.9)',
        width: 4,
        dash:'dashdot',
        
      }
    }]
      };

      Plotly.newPlot("line_imports", traces_, layout);
    },
  },
  watch: {
    imports: function (newVal) {
      this.imports_linechart();
    },
  },
};
</script>

<template>
  <div id="line_imports"></div>
</template>

<style>
.selector-text{
    fill: black !important
}
</style>
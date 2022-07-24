<script>
import * as d3 from "https://cdn.skypack.dev/d3@7";
export default {
  name: "LineEurostat",
  props: {},
  data() {
    return {
      eurostat_ghg: {},
    };
  },
  mounted() {
    Promise.all([d3.csv("./static/eurostat_GHG emissions_clean.csv")]).then(
      (csvs) => {
        this.eurostat_ghg = csvs[0];
      }
    );
  },
  methods: {
    eurostat_ghg_linechart() {
      var names = this.eurostat_ghg.map((d) => d.Label);

      var traces_ = [];
      for (let i = 0; i < names.length; i++) {
        var yrs = Object.keys(this.eurostat_ghg[i]).slice(0, -1);
        var values = Object.values(this.eurostat_ghg[i]).slice(0, -1);

        var temp_trace = {
          x: yrs,
          y: values,
          name: names[i],
          mode: "lines+markers",
        };

        traces_.push(temp_trace);
      }

      traces_.push({x: [2016,2016],y: [3.5,2.5], text:['Obiettivi Italia 2030','(da 9.2 a 4.2)'], textposition:'bottom',mode:'text', showlegend:false, textfont:{size:18,color:'white'}})

      var layout = {
        title:
          "Emissioni Gas effetto serra (in tonnellate equivalenti di CO2, EUROSTAT)",
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

                shapes: [

    //line vertical

    {
      type: 'line',
      x0: 1990,
      y0: 4.2,
      x1: 2020,
      y1: 4.2,
      line: {
        color: 'rgba(227,26,28,0.9)',
        width: 4,
        dash:'dashdot',
        
      }
    }]
      };

      Plotly.newPlot("line_eurostat_ghg", traces_, layout);
      
    },
  },
  watch: {
    eurostat_ghg: function (newVal) {
      this.eurostat_ghg_linechart();
    },
  },
};
</script>

<template>
  <div id="line_eurostat_ghg"></div>
</template>
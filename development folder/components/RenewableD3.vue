<script>
import * as d3 from "https://cdn.skypack.dev/d3@7";
export default {
  name: "RenewableD3",
  props: {},
  data() {
    return {
      top_data: Array,
      top_txt_idx: Array,
      side_data_eol: Array,
      side_data_fv: Array,
      grey_bars: Array,
      grey_idx: Array,
      yrs: Array,
      labels: Array,
      colors: Array,
      changes_txt: [],
    };
  },
  mounted() {
    this.top_data = ["4", "27", "33", "40", "70", "200"];
    this.side_data_eol = [4, 9, 11];
    this.side_data_fv = [0, 18, 22];
    this.grey_bars = [40, 70, 200];
    this.grey_idx = [
      "duemilatrenta_pniec",
      "duemilatrenta_green_deal",
      "duemilacinquanta",
    ];
    this.yrs = ["2008", "2013", "2020", "2030", "2030", "2050 "];
    this.yrs_contd = ["", "", "", "PNIEC", "Green Deal", "Lungo termine"];
    this.idxs_eol_fv = ["duemilaotto", "duemilatredici", "duemilaventi"];
    this.top_txt_idx = [
      "duemilaotto",
      "duemilatredici",
      "duemilaventi",
      "duemilatrenta_pniec",
      "duemilatrenta_green_deal",
      "duemilacinquanta",
    ];

    this.labels = ["Fotovoltaico", "Eolico", "Obiettivi di policy"];
    this.colors = ["rgb(12,240,233)", "orange", "grey"];
    this.changes_txt = ["+4,6 GW/anno", "+0,8 GW/anno"];

  },
  methods: {
    renewable_rects() {
      var width_svg = 600;
      var height_svg = 500;
      var bar_width = 50;
      var bar_space = 90;

      d3.select("#svg_container_ren")
        .append("svg")
        .attr("id", "ren_d3")
        .attr("width", 800)
        .attr("height", 550);
      var svg = d3.select("#ren_d3");

      svg
        .selectAll(".ren_rects_eol")
        .data(this.side_data_eol)
        .enter()
        .append("rect")
        .attr("class", "ren_rects_eol")
        .attr("id", (d, i) => String(this.idxs_eol_fv[i]) + "_eol")
        .attr("x", (d, i) => i * bar_space)
        .attr("y", (d, i) => 0)
        .attr("height", (d) => d * 2)
        .attr("width", bar_width)
        .attr("stroke", (d, i) => this.colors[0])
        .attr("fill", (d, i) => this.colors[0])
        .attr("transform", "translate(200,460)");

      svg.select("#duemilaotto_eol").attr("transform", "translate(200,475)");
      svg.select("#duemilatredici_eol").attr("transform", "translate(200,465)");

      svg
        .selectAll(".ren_rects_fv")
        .data(this.side_data_fv)
        .enter()
        .append("rect")
        .attr("class", "ren_rects_fv")
        .attr("id", (d, i) => String(this.idxs_eol_fv[i]) + "_fv")
        .attr("x", (d, i) => i * bar_space)
        .attr("y", (d, i) => 0)
        .attr("height", (d) => d * 2)
        .attr("width", bar_width)
        .attr("stroke", (d, i) => this.colors[1])
        .attr("fill", (d, i) => this.colors[1])
        .attr("transform", "translate(200,415)");

      svg.select("#duemilatredici_fv").attr("transform", "translate(200,427.9)");

      svg
        .selectAll(".ren_rects_prospect")
        .data(this.grey_bars)
        .enter()
        .append("rect")
        .attr("class", "ren_rects_prospect")
        .attr("id", (d, i) => String(this.grey_idx[i]) + "_prospect")
        .attr("x", (d, i) => i * bar_space)
        .attr("y", (d, i) => 0)
        .attr("height", (d) => d * 2)
        .attr("width", bar_width)
        .attr("stroke", "grey")
        .attr("fill", "grey")
        .attr("transform", "translate(470,80)");


        //side text
              svg
        .selectAll(".texts_side_eol")
        .data(this.side_data_eol)
        .enter()
        .append("text")
        .attr("class", ".texts_side_eol")
        .attr("id", (d, i) =>  "side_data_eol_"+i)
        .attr("x", (d, i) => i * bar_space)
        .attr("y", 403)
        .attr("fill", "white")
        .attr('font-size',12)
        .attr('fill',this.colors[0])
        .text((d, i) => d)
        .attr("transform", "translate(255,80)");

                      svg
        .selectAll(".texts_side_fv")
        .data(this.side_data_fv)
        .enter()
        .append("text")
        .attr("class", ".texts_side_fv")
        .attr("id", (d, i) =>  "side_data_fv_"+i)
        .attr("x", (d, i) => i * bar_space)
        .attr("y", 370)
        .attr("fill", "white")
        .attr('font-size',12)
        .attr("fill", d=> d==0 ?"transparent" :this.colors[1])
        .text((d, i) => d)
        .attr("transform", "translate(255,80)");
        
        //

      svg
        .select("#duemilatrenta_pniec_prospect")
        .attr("transform", "translate(470,400)");
      svg
        .select("#duemilatrenta_green_deal_prospect")
        .attr("transform", "translate(470,340.5)");

      svg
        .selectAll(".texts_top_ren")
        .data(this.top_data)
        .enter()
        .append("text")
        .attr("class", ".texts_top_ren")
        .attr("id", (d, i) => this.top_txt_idx[i] + "_txt_top_ren")
        .attr("x", (d, i) => i * bar_space)
        .attr("y", 250)
        .attr("fill", "white")
        .text((d, i) => d)
        .attr("transform", "translate(215,80)");

      svg
        .select("#duemilaotto_txt_top_ren")
        .attr("transform", "translate(220,220)");
      svg
        .select("#duemilatredici_txt_top_ren")
        .attr("transform", "translate(220,170)");
      svg
        .select("#duemilaventi_txt_top_ren")
        .attr("transform", "translate(220,160)");
      svg
        .select("#duemilatrenta_pniec_txt_top_ren")
        .attr("transform", "translate(220,145)");
      svg
        .select("#duemilatrenta_green_deal_txt_top_ren")
        .attr("transform", "translate(220,85)");
      svg
        .select("#duemilacinquanta_txt_top_ren")
        .attr("transform", "translate(215,-175)");

      svg
        .selectAll(".texts_bottom_ren")
        .data(this.yrs)
        .enter()
        .append("text")
        .attr("class", ".texts_top_ren")
        .attr("id", (d, i) => this.top_txt_idx[i] + "_txt_bottom_ren")
        .attr("x", (d, i) => i * bar_space)
        .attr("y", 250)
        .attr("fill", "white")
        .text((d, i) => d)
        .attr("transform", "translate(200,250)");

      svg
        .selectAll(".texts_bottom_ren_contd")
        .data(this.yrs_contd)
        .enter()
        .append("text")
        .attr("class", ".texts_bottom_ren_contd")
        .attr("id", (d, i) => this.top_txt_idx[i] + "_txt_bottom_ren_contd")
        .attr("x", (d, i) => i * bar_space + 150)
        .attr("y", 250)
        .attr("fill", "white")
        .text((d, i) => d)
        .attr("font-size", 12)
        .attr("transform", "translate(50,270)");

      //add annot
      svg
        .append("path")
        .attr("d", "M200 250 L200 180 L290 180 L290 200")
        .attr("stroke", "white")
        .attr("stroke-dasharray", "4")
        .attr("fill", "rgba(0,0,0,0)")
        .attr("transform", "translate(20,200)");

      svg
        .append("path")
        .attr("d", "M200 250 L200 220 L285 220 L285 245")
        .attr("stroke", "white")
        .attr("stroke-dasharray", "4")
        .attr("fill", "rgba(0,0,0,0)")
        .attr("transform", "translate(125,150)");

      svg
        .selectAll(".changes_txt_ren")
        .data(this.changes_txt)
        .enter()
        .append("text")
        .attr("class", ".changes_txt_ren")
        .attr("id", (d, i) => this.top_txt_idx[i] + "_txt_changes_ren")
        .attr("x", (d, i) => i * bar_space + 15)
        .attr("y", 250)

        .attr("fill", "white")
        .text((d, i) => d)
        .attr("font-size", 12)
        .attr("transform", "translate(210,120)");

      svg
        .select("#duemilatredici_txt_changes_ren")
        .attr("transform", "translate(225,110)");

      svg
        .append("rect")
        .attr("id", "rect_legend")
        .attr("x", 0)
        .attr("y", 0)
        .attr("width", 180)
        .attr("height", height_svg)
        .attr("stroke", "rgba(0,136,174,255)")
        .attr("fill", "rgba(0,136,174,255)");

      var sun = [
        "M30,13.21A3.93,3.93,0,1,1,36.8,9.27L41.86,18A3.94,3.94,0,1,1,35.05,22L30,13.21Zm31.45,13A35.23,35.23,0,1,1,36.52,36.52,35.13,35.13,0,0,1,61.44,26.2ZM58.31,4A3.95,3.95,0,1,1,66.2,4V14.06a3.95,3.95,0,1,1-7.89,0V4ZM87.49,10.1A3.93,3.93,0,1,1,94.3,14l-5.06,8.76a3.93,3.93,0,1,1-6.81-3.92l5.06-8.75ZM109.67,30a3.93,3.93,0,1,1,3.94,6.81l-8.75,5.06a3.94,3.94,0,1,1-4-6.81L109.67,30Zm9.26,28.32a3.95,3.95,0,1,1,0,7.89H108.82a3.95,3.95,0,1,1,0-7.89Zm-6.15,29.18a3.93,3.93,0,1,1-3.91,6.81l-8.76-5.06A3.93,3.93,0,1,1,104,82.43l8.75,5.06ZM92.89,109.67a3.93,3.93,0,1,1-6.81,3.94L81,104.86a3.94,3.94,0,0,1,6.81-4l5.06,8.76Zm-28.32,9.26a3.95,3.95,0,1,1-7.89,0V108.82a3.95,3.95,0,1,1,7.89,0v10.11Zm-29.18-6.15a3.93,3.93,0,0,1-6.81-3.91l5.06-8.76A3.93,3.93,0,1,1,40.45,104l-5.06,8.75ZM13.21,92.89a3.93,3.93,0,1,1-3.94-6.81L18,81A3.94,3.94,0,1,1,22,87.83l-8.76,5.06ZM4,64.57a3.95,3.95,0,1,1,0-7.89H14.06a3.95,3.95,0,1,1,0,7.89ZM10.1,35.39A3.93,3.93,0,1,1,14,28.58l8.76,5.06a3.93,3.93,0,1,1-3.92,6.81L10.1,35.39Z",
      ];
      var wind = [
        `M306.069,189.427H7.5c-4.143,0-7.5-3.358-7.5-7.5s3.357-7.5,7.5-7.5h297.119c0.469-0.092,0.954-0.14,1.45-0.14
			c24.47,0,44.378-19.908,44.378-44.378S330.539,85.53,306.069,85.53s-44.378,19.908-44.378,44.378c0,4.142-3.357,7.5-7.5,7.5
			s-7.5-3.358-7.5-7.5c0-32.741,26.637-59.378,59.378-59.378s59.378,26.637,59.378,59.378c0,32.224-25.801,58.535-57.829,59.358
			C307.118,189.372,306.601,189.427,306.069,189.427z`,
        `M152.283,137.479H7.5c-4.143,0-7.5-3.358-7.5-7.5s3.357-7.5,7.5-7.5h143.333c0.469-0.092,0.954-0.14,1.45-0.14
			c24.47,0,44.378-19.908,44.378-44.378s-19.908-44.378-44.378-44.378c-24.471,0-44.379,19.908-44.379,44.378
			c0,4.142-3.357,7.5-7.5,7.5s-7.5-3.358-7.5-7.5c0-32.741,26.638-59.378,59.379-59.378s59.378,26.637,59.378,59.378
			c0,32.224-25.801,58.535-57.829,59.358C153.332,137.423,152.814,137.479,152.283,137.479z`,
        `M244.186,346.866c-32.741,0-59.379-26.637-59.379-59.378c0-4.142,3.357-7.5,7.5-7.5s7.5,3.358,7.5,7.5
			c0,24.47,19.908,44.378,44.379,44.378c24.47,0,44.378-19.908,44.378-44.378s-19.908-44.378-44.378-44.378H7.5
			c-4.143,0-7.5-3.358-7.5-7.5s3.357-7.5,7.5-7.5h236.686c32.741,0,59.378,26.637,59.378,59.378S276.927,346.866,244.186,346.866z`,
      ];

      svg
        .append("circle")
        .attr("r", 20)
        .attr("cx", 30)
        .attr("cy", 150)
        .attr("stroke", "light")
        .attr("fill", "orange");

      svg
        .append("circle")
        .attr("r", 20)
        .attr("cx", 30)
        .attr("cy", 250)
        .attr("stroke", this.colors[0])
        .attr("fill", this.colors[0]);

      svg
        .append("circle")
        .attr("r", 20)
        .attr("cx", 30)
        .attr("cy", 350)
        .attr("stroke", "grey")
        .attr("fill", "grey");

      for (var each of sun) {
        svg
          .append("path")
          .attr("d", each)
          .attr("stroke", "yellow")
          .attr("fill", "yellow")
          .attr("transform", "translate(14.5,134),scale(0.25)");
      }

      for (var each of wind) {
        svg
          .append("path")
          .attr("d", each)
          .attr("stroke", "lightblue")
          .attr("fill", "lightblue")
          .attr("transform", "translate(14.5,233),scale(0.09)");
      }

      svg
        .selectAll(".labels_legend_ren")
        .data(this.labels)
        .enter()
        .append("text")
        .attr("class", ".labels_legend_ren")
        .attr("id", (d, i) => d + "_label_legend_ren")
        .attr("x", 55)
        .attr("y", (d, i) => i * 100)
        .attr("fill", "white")
        .text((d, i) => d)
        .attr("font-size", 12)
        .attr("transform", "translate(0,153)");



      var legend_decoration_bottom = [
        `M200,122c-11.869,0-21.725,8.668-23.639,20H160c-13.234,0-24,10.76-24,23.986V210h8v-44.014
				c0-8.814,7.178-15.986,16-15.986h16.361c1.913,11.332,11.77,20,23.639,20c13.234,0,24-10.766,24-24S213.234,122,200,122z
				 M200,162c-8.822,0-16-7.178-16-16s7.178-16,16-16s16,7.178,16,16S208.822,162,200,162z`,
        `M163.461,94h12.9c1.913,11.332,11.77,20,23.639,20c13.234,0,24-10.766,24-24s-10.766-24-24-24
				c-11.869,0-21.725,8.668-23.639,20h-12.9c-6.297,0-11.42-5.125-11.42-11.426V14h-8v60.574C144.041,85.285,152.752,94,163.461,94z
				 M200,74c8.822,0,16,7.178,16,16s-7.178,16-16,16s-16-7.178-16-16S191.178,74,200,74z`,
      ];
      var legend_decoration_top= [ `M92,62H76c0-13.234-10.766-24-24-24S28,48.766,28,62s10.766,24,24,24c10.426,0,19.295-6.693,22.6-16H92
				c8.822,0,16,7.188,16,16.024V210h8V86.024C116,72.777,105.234,62,92,62z M52,78c-8.822,0-16-7.178-16-16s7.178-16,16-16
				s16,7.178,16,16S60.822,78,52,78z`,
        `M64,122H47.639c-1.914-11.332-11.77-20-23.639-20c-13.234,0-24,10.766-24,24s10.766,24,24,24
				c11.869,0,21.725-8.668,23.639-20H64c8.822,0,16,7.184,16,16.014V210h8v-63.986C88,132.774,77.234,122,64,122z M24,142
				c-8.822,0-16-7.178-16-16s7.178-16,16-16s16,7.178,16,16C40,134.822,32.822,142,24,142z`,]

      
      for (var each of legend_decoration_top) {
        svg
          .append("path")
          .attr("d", each)
          .attr("stroke", "#212529 ")
          .attr("fill", "#212529 ")
          .attr('opacity',0.5)
          .attr("transform", "translate(125,-40),scale(1)");
      }
      for (var each of legend_decoration_bottom) {
        svg
          .append("path")
          .attr("d", each)
          .attr("stroke", "#212529 ")
          .attr("fill", "#212529 ")
          .attr('opacity',0.5)
          .attr("transform", "translate(-160,360),scale(1)");
      }
      var legend_title = ["Piano sviluppo rinnovabili"];
            svg
        .append("text")
        .text(legend_title)
        .attr("x", 5)
        .attr("y", 30)
        .attr("fill", "white")
        .attr("stroke", "white");
    },

    
  },
  watch: {
    changes_txt: function (newVal) {
      this.renewable_rects();
    },
  },
};
</script>

<template>
  <div id="svg_container_ren"></div>
</template>
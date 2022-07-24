<script>
import * as d3 from "https://cdn.skypack.dev/d3@7";
import * as topojson from "https://cdn.skypack.dev/topojson@3.0.2";
import chroma from "chroma-js";
var width_svg = 700;
var height_svg = 500;
var center_open_list=9.5
var center_closed_list=5

var color_scales = [
  d3.schemePaired,
  d3.schemeCategory10,
  d3.schemeTableau10,
  d3.schemeSet1,
];

//let colors =["#2c7bb6", "#00a6ca","#00ccbc","#90eb9d","#ffff8c","#f9d057","#f29e2e","#e76818","#d7191c",'#1B676B', '#519548', '#88C425', "#BEF202", "#EAFDE6",'#69D2E7', '#A7DBD8', '#E0E4CC', '#F38630', '#FA6900', '#FF4E50', '#F9D423',"#2c7bb6", "#00a6ca","#00ccbc","#90eb9d","#ffff8c","#f9d057","#f29e2e","#e76818","#d7191c"]
var colors = [
  ...new Set(
    [].concat(
      d3.schemePaired,
      d3.schemeCategory10,
      d3.schemeTableau10,
      d3.schemeSet1,
      d3.schemeDark2,
      d3.schemeSet2,
      d3.schemeAccent,
      d3.schemePastel1,
      d3.schemePastel2,
      d3.schemeSet3,
      chroma.scale("RdYlBu"),
      chroma.scale("bezier-YGnBu"),
      chroma.scale("hot")
    )
  ),
];
colors = colors.sort(() => Math.random() - 0.5);
var border_colors = [
  ...new Set(
    [].concat(
      chroma.scale("RdYlBu"),
      chroma.scale("bezier-YGnBu"),
      chroma.scale("hot"),
      d3.schemeSet3,
      d3.schemePastel1,
      d3.schemePastel2,
      d3.schemeAccent,
      d3.schemeSet2,
      d3.schemeDark2,
      d3.schemeSet1,
      d3.schemeTableau10,
      d3.schemeCategory10,
      d3.schemePaired
    )
  ),
];
border_colors = border_colors.sort(() => Math.random() - 0.5);
("COLORS FULL", colors, border_colors);

var projection;

var legend_dicts = {
  "Centrali stoccaggio": [],
  "Centrali termoelettriche": [],
  "Pozzi storici": [],
  "Pozzi stoccaggio": [],
};

var legend_dicts_strokes = {
  "Centrali stoccaggio": [],
  "Centrali termoelettriche": [],
  "Pozzi storici": [],
  "Pozzi stoccaggio": [],
};

export default {
  name: "map_container",
  props: { filters: Array, layers: Array, sel_type: String,start:Boolean },
  data() {
    return {
      world_map: [],

      transmed: [],
      norvegian: [],
      ru: [],
      greenstream: [],
      scp: [],
      pipes_names: [],
      pipes_arcs: [],

      centrali_stoc: [],
      pozzi_stoc: [],
      centrali_term: [],
      pozzi_storici: [],

      point_list: [],
      badge_list: [],

      list_active:true,
      center_map:Number,

      //gestisco manualmente "Pozzi storici" per ridurre il tempo di creazione layer
      pozzi_idr_descr: [],
      pozzi_idr_filtered_all: {},
      idr_filt_sets: {},
      pozzi_idr_filtered_all_fills: {},
      pozzi_idr_filtered_all_strokes: {},

      pozzi_stor_descr: [],
      pozzi_stor_filtered_all: {},
      stor_filt_sets: {},
      pozzi_stor_filtered_all_fills: {},
      pozzi_stor_filtered_all_strokes: {},

      zoom: true,
      projection: projection,
      active_legend: "Centrali stoccaggio",
      mise_stoc_centrals_csv: {},
    };
  },
  mounted() {
    Promise.all([
      d3.json("./static/map_layers.json.json"),
      d3.json("./static/temp_map.json.json"),
      d3.json("./static/Pozzi_storici-point.json"),
    ]).then((data) => {
      //console.log("TOPOJSON", data[0].objects);
      this.world_map = topojson.feature(
        data[1],
        data[1].objects.ne_50m_admin_0_countries
      ).features;

      this.greenstream = topojson.feature(
        data[0],
        data[0].objects.Greenstream_Libia_Siclia
      ).features;
      this.scp = topojson.feature(
        data[0],
        data[0].objects.SCP_TANAS_TAP_Azerbaijan_Puglia
      ).features;
      this.ru = topojson.feature(
        data[0],
        data[0].objects.RU_Italia_pipeline
      ).features;
      this.norvegian = topojson.feature(
        data[0],
        data[0].objects.Norvegia_Olanda_Piemonte
      ).features;
      this.transmed = topojson.feature(
        data[0],
        data[0].objects.Transmed_Pipeline_Algeria_Sicilia
      ).features;
      this.centrali_stoc = topojson.feature(
        data[0],
        data[0].objects.Centrali_stoccaggio
      ).features;
      this.pozzi_stoc = topojson.feature(
        data[0],
        data[0].objects.Pozzi_stoccaggio
      ).features;
      this.pozzi_idrocarburi = topojson.feature(
        data[0],
        data[0].objects.Pozzi_idrocarburi
      ).features;
      this.centrali_term = topojson.feature(
        data[0],
        data[0].objects.Centrali_termiche
      ).features;
      this.pozzi_storici = topojson.feature(
        data[2],
        data[2].objects.Pozzi_storici
      ).features;
      //run at first
      if (this.zoom == true) {
        this.zoom_in();
      } else if (this.zoom == false) {
        this.zoom_out();
      }
      this.draw_legend()
    });
    
  }  ,
  methods: {

    handle_selection_type() {
      if (this.sel_type == "Singolo") {
        document.getElementById("layer_selection").multiple = false;
      } else if (this.sel_type == "Multiplo") {
        document.getElementById("layer_selection").multiple = true;
        var el_list = document.getElementById("layer_selection").options;
        for (let i = 0; i < el_list.length; i++) {
          if (this.layers.includes(el_list[i].value)) {
            el_list[i].selected = "selected";
          } else {
          }
        }
      }

    },
    zoom_in() {
      this.active_legend=this.layers[0]
      this.zoom = true;
      this.point_list = [];
      this.badge_list = [];
      if (this.list_active==true){
        this.center_map=center_open_list
      } else if (this.list_active==false){
        this.center_map=center_closed_list
      }
      var proj_ = d3
        .geoMercator()
        .center([this.center_map, 42])
        .scale(2000)
        .translate([width_svg / 2, height_svg / 2]);

      this.draw_piplines(proj_);
    },
    zoom_out() {
      this.zoom = false;
      this.point_list = [];
      this.badge_list = [];
            if (this.list_active==true){
        this.center_map=center_open_list
      } else if (this.list_active==false){
        this.center_map=center_closed_list
      }
      var proj_ = d3
        .geoMercator()
        .center([15, 40])
        .scale(1000)
        .translate([width_svg / 2, height_svg / 2]);
      this.draw_piplines(proj_);
    },
    draw_legend() {

      //if (this.active_legend != "Nessuna") { // non c'è più "Nessuna", rimuovere condizione
      d3.selectAll('[class*="legend"]').remove();
      d3.selectAll('[class*="labels"]').remove();
      d3.select(".legend_container").remove();
      d3.select("#meta_map").append("div").attr("class", "legend_container");
      d3.select(".legend_container")
        .append("svg")
        .attr("class", "legend_svg")
        .attr("height", 3000)
        .attr("width", 400);
      var svg = d3.select(".legend_svg");

      // CONTINUA DA QUI! (problema con il select della legenda)
/*
      if (this.sel_type == "Multiplo") {
        this.active_legend = this.layers[0];
      } else if (this.sel_type == "Singolo") {
        this.active_legend = this.layers[0];
      }
*/    



      if (
        (this.active_legend == "Centrali stoccaggio") &
        this.layers.includes("Centrali stoccaggio")
      ) {
        var names_ = Object.keys(legend_dicts["Centrali stoccaggio"]);
        var colors_ = Object.values(legend_dicts["Centrali stoccaggio"]);
        var border_colors_ = Object.values(
          legend_dicts_strokes["Centrali stoccaggio"]
        );
        //console.log("LISTS LEGENDS", names_, colors_);
        //legend
        svg
          .selectAll(".centrali_stoc_legend")
          .data(names_)
          .enter()
          .append("polygon")
          .attr("class", "centrali_stoc_legend")
          .attr("points", function (d, i) {
            return `12,${
              10 + i * (15 + 5)
            } 5,${19 + i * (15 + 5) + 5} 20, ${24 + i * (15 + 5)} `;
          })
          .attr("stroke", (d, i) => border_colors_[i])
          .attr("fill", (d, i) => colors_[i]);
        //text
        svg
          .selectAll(".centrali_stoc_lables")
          .data(names_)
          .enter()
          .append("text")
          .attr("class", "centrali_stoc_labels")
          .attr("x", 0 + 15 * 2)
          .attr("y", function (d, i) {
            return 15 + i * (15 + 5) + 10 / 2;
          })
          .attr("stroke", "black")
          .attr("fill", (d, i) => colors_[i])
          .text((d) => d)
          .attr("text-anchor", "left");
        //list
        /*
        this.point_list.push(
          this.centrali_stoc.map((d, i) => [
            "Centrale stoc",
            d.properties.Name,
            d.properties.descriptio.split(";"),
          ])
        );*/
      } else if (
        (this.active_legend == "Centrali termoelettriche") &
        this.layers.includes("Centrali termoelettriche")
      ) {
        var names_ = Object.keys(legend_dicts["Centrali termoelettriche"]);
        var colors_ = Object.values(legend_dicts["Centrali termoelettriche"]);
        var border_colors_ = Object.values(
          legend_dicts_strokes["Centrali termoelettriche"]
        );
        
        //console.log("LISTS LEGENDS", names_, colors_);
        //legend
        svg
          .selectAll(".centrali_term_legend")
          .data(names_)
          .enter()
          .append("rect")
          .attr("class", "centrali_term_legend")
          .attr("x", 0)
          .attr("y", function (d, i) {
            return 6 + i * (10 + 5);
          })
          .attr("width", 10)
          .attr("height", 10)
          .attr("stroke", (d, i) => border_colors_[i])
          .attr("fill", (d, i) => colors_[i]);
        //text
        svg
          .selectAll(".centrali_term_lables")
          .data(names_)
          .enter()
          .append("text")
          .attr("class", "centrali_term_labels")
          .attr("x", 0 + 10 * 2)
          .attr("y", function (d, i) {
            return 10 + i * (10 + 5) + 10 / 2;
          })
          .attr("stroke", "black")
          .attr("fill", (d, i) => colors_[i])
          .text((d) => d)
          .attr("text-anchor", "left");
        //list
        /*
        this.point_list.push(
          this.centrali_term.map((d, i) => [
            "Centrale term",
            d.properties.Name,
            d.properties.descriptio.split(";"),
          ])
        );*/
      } else if (
        (this.active_legend == "Pozzi storici") &
        this.layers.includes("Pozzi storici")
      ) {
        var names_ = this.stor_filt_sets[this.filters[2][0]];

        var temp_dict_fills = {};
        var temp_dict_strokes = {};

        names_.map((d, i) => (temp_dict_fills[d] = colors[i]));
        names_.map((d, i) => (temp_dict_strokes[d] = border_colors[i]));

        var colors_ = Object.values(temp_dict_fills);
        var border_colors_ = Object.values(temp_dict_strokes);

        if (this.filters[2][0]=='Anno'){

          colors_= d3.scaleLinear().domain([0,30,60,90,123]).range(["black","blue", "green", "yellow", "red"])
          border_colors_= d3.scaleLinear().domain([1,123]).range(["white", "blue"])
        }
        //console.log("LISTS LEGENDS", names_, colors_);
        //legend
        svg
          .selectAll(".pozzi_idr_legend")
          .data(names_)
          .enter()
          .append("circle")
          .attr("class", "pozzi_idr_legend")
          .attr("cx", 10)
          .attr("cy", function (d, i) {
            return 10 + i * (10 + 5);
          })
          /*.attr("width", 12)
          .attr("height", 6)*/
          .attr("r", 6)
          .attr("stroke", (d, i) => this.filters[2][0]!='Anno' ?border_colors_[i] :'black')
          .attr("fill", (d, i) => this.filters[2][0]!='Anno' ?colors_[i] :colors_(i))
          .attr("stroke-width", 3);
        //text
        svg
          .selectAll(".pozzi_idr_labels")
          .data(names_)
          .enter()
          .append("text")
          .attr("class", "pozzi_idr_labels")
          .attr("x", 0 + 10 * 2)
          .attr("y", function (d, i) {
            return 10 + i * (10 + 5) + 10 / 2;
          })
          .attr("stroke", "black")
          .attr("fill", 'black')
          .text((d) => d)
          .attr("text-anchor", "left");
        //list
        /*
        this.point_list.push(
          this.pozzi_idrocarburi.map((d, i) => [
            "Pozzo idr",
            d.properties.Name,
            d.properties.descriptio.split(";"),
          ])
        );*/
      } else if (
        (this.active_legend == "PITESAI") &
        this.layers.includes("PITESAI")
      ) {
        var names_ = ["Idoneo", "Non ideoneo"];
        var colors_ = ["green", "grey"];
        var border_colors_ = ["black", "black"];

        //console.log("LISTS LEGENDS", names_, colors_);
        //legend
        svg
          .selectAll(".PITESAI_legend")
          .data(names_)
          .enter()
          .append("rect")
          .attr("class", "PITESAI_legend")
          .attr("x", 0)
          .attr("y", function (d, i) {
            return 6 + i * (10 + 5);
          })
          .attr("width", 10)
          .attr("height", 10)
          .attr("stroke", (d, i) => border_colors_[i])
          .attr("fill", (d, i) => colors_[i])
          .attr("stroke-width", 2);
        //text
        svg
          .selectAll(".pozzi_stoc_lables")
          .data(names_)
          .enter()
          .append("text")
          .attr("class", "pozzi_stoc_labels")
          .attr("x", 0 + 10 * 2)
          .attr("y", function (d, i) {
            return 12 + i * (10 + 5) + 10 / 2;
          })
          .attr("stroke", "black")
          .attr("fill", "black")
          .text((d) => d)
          .attr("text-anchor", "left");
        //list
        /*
        this.point_list.push(
          this.pozzi_stoc.map((d, i) => [
            "Pozzo stoc",
            d.properties.Name,
            d.properties.descriptio.split(";"),
          ])
        );*/
      }
      //} //else {
      //d3.select(".legend_container").remove();
      //}
      //console.log(this.active_legend);
    },
    check_filter(
      type,
      filter,
      description,
      default_color,
      default_size,
      data,
      name,
      d3_attr,
      idx,
      dbl_name
    ) {
      var filtered_data = [];
      //console.log("START_FILT", filter);
      //console.log('DESCRIP',description)
      for (let i = 0; i < description.length; i++) {
        if (!description[i].includes(":")) {
          description[i] += ":1";
        }
      }
      //console.log('DESCRIP2',description)
      if (name != "Pozzi storici") {
        if (filter.length > 3) {
          for (var el of description) {
            if (el.split(":")[0].replace("'", "").trim() == filter) {
              //console.log(typeof el, "TARGG");

              filtered_data.push(el.split(":")[1].trim()); //.trim()
            }
          }

          if (type == "color") {
            var filtered_set_temp = data.map((d) =>
              d.properties.descriptio.split(";")
            );
            filtered_set_temp = filtered_set_temp.map((d) =>
              d.map((k) => k.split(":"))
            );
            filtered_set_temp = filtered_set_temp.map((d) =>
              d.map((k) => (k[0].trim() == filter.trim() ? k[1] : null))
            );
            filtered_set_temp = filtered_set_temp.map((d) =>
              d.filter((k) => k != null)
            );
            filtered_set_temp = filtered_set_temp.flat(1).map((d) => d.trim());
            var filtered_set = [...new Set(filtered_set_temp)].sort();
            //console.log("TEMPP", filtered_set_temp, filtered_set);
            if (d3_attr == "fill") {
              var filtered_dict = {};
              filtered_set.forEach((d, i) => (filtered_dict[d] = colors[i]));
              legend_dicts[name] = filtered_dict;
              return filtered_dict[filtered_data[0]];
            } else if (d3_attr == "stroke") {
              var filtered_dict = {};
              filtered_set.forEach(
                (d, i) => (filtered_dict[d] = border_colors[i])
              );
              legend_dicts_strokes[name] = filtered_dict;
              /*console.log(
              "RESULTT",
              filtered_dict,
              filtered_data,
              filtered_dict[filtered_data[0]]
            );*/
              return filtered_dict[filtered_data[0]];
            }
            var filtered_data_colors = filtered_data.map(
              (d) => filtered_dict[d]
            );
            //console.log(filtered_dict[filtered_data[0]]);
          } else if (type == "size") {
            //console.log("SIZE", filtered_data);
            //console.log("DATA__", filtered_data);
            if (filter.length > 3) {
              if (name == "Centrali termoelettriche") {
                return Math.sqrt(+filtered_data[0]) / 2;
              } else if (name == "Pozzi stor_") {
                return Math.sqrt(+filtered_data[0]) / 10;
              }
            } else {
              return 5;
            }
          }
        } else {
          //console.log("DEFAULT", default_color, default_size);
          filtered_data.push(default_color);
          filtered_data.push(default_size);
          return filtered_data;
        }
      } else if (name == "Pozzi storici") {
        /*console.log(
          "AAAAA",
          this.pozzi_stor_filtered_all_fills,
          this.pozzi_idr_filtered_all,
          filter
        );*/
        if (filter.length > 3) {
          if (type == "size") {
            //console.log(filtered_data);
            return Math.sqrt(+filtered_data[0] / 50);
          }
          if (d3_attr == "fill") {
            return this.pozzi_stor_filtered_all_fills[filter][idx];
          } else if ((d3_attr = "stroke")) {
            return this.pozzi_stor_filtered_all_strokes[filter][idx];
          }
        } else {
          //console.log("DEFAULT", default_color, default_size);
          return [default_color, 3];
        }
      }
    },
    draw_piplines(projection) {
      d3.selectAll("#pipelines_map").remove();
      /*    d3.selectAll(".world").remove();
      d3.selectAll(".pipe").remove();
      d3.selectAll("#pitesai").remove();
      d3.selectAll(".pozzi_stoccaggio").remove();
      d3.selectAll(".pozzi_idrocarburi").remove();
      d3.selectAll(".centrali_stoccaggio").remove()
      ;*/

      var svg = d3
        .select("#pipelines_map_container")
        .append("svg")
        .attr("id", "pipelines_map")
        .attr("width", width_svg)
        .attr("height", height_svg);
      //draw paths from piplines arcs
      /*console.log(
        "LENGHT",
        document.getElementsByName("pipelines_map_container")
      );*/
      var path = d3.geoPath().projection(projection);

      svg
        .selectAll("world")
        .data(this.world_map)
        .enter()
        .append("path")
        .attr("class", "world")
        .attr("d", path)
        .attr("stroke", "black")
        .attr("fill", "white");

      if (this.layers.includes("Gasdotti")) {
        svg
          .selectAll("pipe")
          .data(this.transmed)
          .enter()
          .append("path")
          .attr("class", "pipe")
          .attr("id", "transmed")
          .attr("d", path)
          .attr("stroke", "red")
          .attr("fill", "rgba(0,0,0,0)")
          .attr("stroke-width", 1);

        svg
          .selectAll("pipe")
          .data(this.ru)
          .enter()
          .append("path")
          .attr("class", "pipe")
          .attr("id", "ru")
          .attr("d", path)
          .attr("stroke", "red")
          .attr("fill","rgba(0,0,0,0)")
          .attr("stroke-width", 1);

        svg
          .selectAll("pipe")
          .data(this.scp)
          .enter()
          .append("path")
          .attr("class", "pipe")
          .attr("id", "scp")
          .attr("d", path)
          .attr("stroke", "red")
          .attr("fill", "rgba(0,0,0,0)")
          .attr("stroke-width", 1);

        svg
          .selectAll("pipe")
          .data(this.greenstream)
          .enter()
          .append("path")
          .attr("class", "pipe")
          .attr("id", "greenstream")
          .attr("d", path)
          .attr("stroke", "red")
          .attr("fill", "rgba(0,0,0,0)")
          .attr("stroke-width", 1);

        svg
          .selectAll("pipe")
          .data(this.norvegian)
          .enter()
          .append("path")
          .attr("class", "pipe")
          .attr("id", "norvegian")
          .attr("d", path)
          .attr("stroke", "red")
          .attr("fill", "rgba(0,0,0,0)")
                    .attr("stroke-width", 1);

         ;

      
      }
      //console.log("POINT LIST", this.point_list);
      if (this.layers.includes("PITESAI")) {
        var x_;
        var y_;
        var width_;
        var height_;
        var proj_x;
        var proj_y;
        let projection_;

        if (this.zoom == false) {
          x_ = 7.6;
          y_ = 46.9;
          width_ = 235.49;
          height_ = 285.27;
          proj_x = 20;
          proj_y = 50;
        } else if (this.zoom == true) {
          x_ = 4.0;
          y_ = 47.2;
          width_ = 460;
          height_ = 530;
          proj_x = 0;
          proj_y = 0;
        }

        svg
          .append("svg:image")
          .attr("x", projection([x_, y_])[0])
          .attr("y", projection([x_, y_])[1])
          .attr("width", width_)
          .attr("height", height_)
          .attr("href", "static/pitesai.png")
          .attr(
            "transform",
            `rotate(3.8,${projection([proj_x, proj_y])[0]},${
              projection([proj_x, proj_y])[1]
            })`
          )
          .attr("id", "pitesai");
      }
      /*console.log(
        "CENTRALI STOCCAGGIO",
        projection(this.centrali_stoc[0].geometry.coordinates)
      );*/
      if (this.layers.includes("Pozzi storici")) {
        //console.log("STORRR", this.pozzi_storici);
        if (this.filters[2][0]=='Anno'){
          var colors__= d3.scaleLinear().domain([1895,1925,1955,1985,2010]).range(["black","blue", "green", "yellow", "red"])
          var border_colors__= d3.scaleLinear().domain([1,123]).range(["white", "blue"])
          var yrs=this.pozzi_stor_descr.map(d=>typeof d[1]  != 'undefined' ?+d[1][1] :1900)
        }

        svg
          .selectAll("pozzi_storici")
          .data(this.pozzi_storici)
          .enter()
          .append("circle")
          .attr("class", "pozzi_storici")
          .attr("cx", (d) => projection(d.geometry.coordinates)[0])
          .attr("cy", (d) => projection(d.geometry.coordinates)[1])
          .attr("stroke", (d, i) =>
            this.filters[2][0]!='Anno'
            ?this.check_filter(
              "color",
              this.filters[2][0],
              d.properties.descriptio.split(";"),
              "blue",
              "3",
              this.pozzi_storici,
              "Pozzi storici",
              "stroke",
              i,
              "Pozzi_"
            )
            :'black'
          )
          .attr("fill", (d, i) =>
            this.filters[2][0]!='Anno'
            ?this.check_filter(
              "color",
              this.filters[2][0],
              d.properties.descriptio.split(";"),
              "blue",
              "3",
              this.pozzi_storici,
              "Pozzi storici",
              "fill",
              i,
              "Pozzi_"
            )
            :colors__(yrs[i])
          )
          .attr("r", (d, i) =>
            this.check_filter(
              "size",
              this.filters[2][1],
              d.properties.descriptio.split(";"),
              "red",
              "3",
              this.pozzi_storici,
              "Pozzi stor_",
              i,
              "Pozzi_"
            ).length == 2
              ? this.check_filter(
                  "size",
                  this.filters[2][1],
                  d.properties.descriptio.split(";"),
                  "red",
                  "3",
                  this.pozzi_storici,
                  "Pozzi stor_",
                  i,
                  "Pozzi_"
                )[1]
              : this.check_filter(
                  "size",
                  this.filters[2][1],
                  d.properties.descriptio.split(";"),
                  "red",
                  "3",
                  this.pozzi_storici,
                  "Pozzi stor_",
                  i,
                  "Pozzi_"
                )
          )
          .attr("stroke-width", 1)
          .attr('opacity',d=>this.filters[2][0] === 'Anno' ?0.2 :0.5)
        /*.attr("height", 6)
          .attr("width", 12);*/

        this.point_list.push(
          this.pozzi_storici.map((d, i) => [
            "Pozzo storico",
            d.properties.Name,
            d.properties.descriptio.split(";").map((d) => d.trim()),
          ])
        );
      }
      if (this.layers.includes("Pozzi stoccaggio")) {
        svg
          .selectAll("pozzi_stoccaggio")
          .data(this.pozzi_stoc)
          .enter()
          .append("polygon")
          .attr("class", "pozzi_stoccaggio")
          //.attr("transform-origin", "center")
          //.attr("transform", `rotate(${45})`)
          //.attr("transform", "scale(1,-1)")
          .attr(
            "points",
            (d) =>
              `${projection(d.geometry.coordinates)[0]},${
                projection(d.geometry.coordinates)[1]
              } ${projection(d.geometry.coordinates)[0] - 5},${
                projection(d.geometry.coordinates)[1] - 5
              } ${projection(d.geometry.coordinates)[0] - 10},${
                projection(d.geometry.coordinates)[1]
              }`
          )
          .attr("stroke", (d) =>
            this.check_filter(
              "color",
              this.filters[3][0],
              d.properties.descriptio.split(";"),
              "violet",
              "3",
              this.pozzi_stoc,
              "Pozzi stoccaggio",
              "stroke"
            ).length == 2
              ? this.check_filter(
                  "color",
                  this.filters[3][0],
                  d.properties.descriptio.split(";"),
                  "violet",
                  "3",
                  this.pozzi_stoc,
                  "Pozzi stoccaggio",
                  "stroke"
                )[0]
              : this.check_filter(
                  "color",
                  this.filters[3][0],
                  d.properties.descriptio.split(";"),
                  "violet",
                  "3",
                  this.pozzi_stoc,
                  "Pozzi stoccaggio",
                  "stroke"
                )
          )
          .attr("fill", (d) =>
            this.check_filter(
              "color",
              this.filters[3][0],
              d.properties.descriptio.split(";"),
              "violet",
              "3",
              this.pozzi_stoc,
              "Pozzi stoccaggio",
              "fill"
            ).length == 2
              ? this.check_filter(
                  "color",
                  this.filters[3][0],
                  d.properties.descriptio.split(";"),
                  "violet",
                  "3",
                  this.pozzi_stoc,
                  "Pozzi stoccaggio",
                  "fill"
                )[0]
              : this.check_filter(
                  "color",
                  this.filters[3][0],
                  d.properties.descriptio.split(";"),
                  "violet",
                  "3",
                  this.pozzi_stoc,
                  "Pozzi stoccaggio",
                  "fill"
                )
          )
          .attr("stroke-width", 1);
        //.attr("height", 6)
        //.attr("width", 3);

        //.attr('transform-box','fill-box')
        //.attr('transform-origin','10% 10%')

        this.point_list.push(
          this.pozzi_stoc.map((d, i) => [
            "Pozzo stoc",
            d.properties.Name,
            d.properties.descriptio.split(";"),
          ])
        );
      }
      if (this.layers.includes("Centrali stoccaggio")) {
        //  console.log("descr prova", this.filters[0][1]);
        var descr_temp = this.centrali_stoc.map((d) =>
          d.properties.descriptio.split(";").map((y) => y.split(":"))
        );
        //descr_temp= descr_temp.map(d=>d[0]==this.filters[0][1] ?d[0].trim() :null)
        descr_temp = descr_temp.map((d) =>
          d.filter((y) => y[0].trim().replace("'", "") == this.filters[0][1])
        );
        /*console.log(
          "descr_temp",
          descr_temp.map((d) => d.map((y) => y[1]))
        );*/
        if (this.filters[0][1].length > 3) {
          var weights = descr_temp.map((d) => d.map((y) => y[1]));
        } else {
          var weights = descr_temp.map((d) => 20);
        }
        svg
          .selectAll("centrali_stoccaggio")
          .data(this.centrali_stoc)
          .enter()
          .append("polygon")
          .attr("class", "centrali_stoccaggio")
          .attr(
            "points",
            (d, i) =>
              `${projection(d.geometry.coordinates)[0]},${
                projection(d.geometry.coordinates)[1]
              } ${
                projection(d.geometry.coordinates)[0] +
                6 -
                Math.log(weights[i]) * 5
              },${
                projection(d.geometry.coordinates)[1] - Math.log(weights[i]) * 5
              } ${
                projection(d.geometry.coordinates)[0] - Math.log(weights[i]) * 5
              },${projection(d.geometry.coordinates)[1]}`
          )
          .attr("stroke", (d) =>
            this.check_filter(
              "color",
              this.filters[0][0],
              d.properties.descriptio.split(";"),
              "red",
              "3",
              this.centrali_stoc,
              "Centrali stoccaggio",
              "stroke"
            ).length == 2
              ? this.check_filter(
                  "color",
                  this.filters[0][0],
                  d.properties.descriptio.split(";"),
                  "red",
                  "3",
                  this.centrali_stoc,
                  "Centrali stoccaggio",
                  "stroke"
                )[0]
              : this.check_filter(
                  "color",
                  this.filters[0][0],
                  d.properties.descriptio.split(";"),
                  "red",
                  "3",
                  this.centrali_stoc,
                  "Centrali stoccaggio",
                  "stroke"
                )
          )
          .attr("fill", (d) =>
            this.check_filter(
              "color",
              this.filters[0][0],
              d.properties.descriptio.split(";"),
              "red",
              "3",
              this.centrali_stoc,
              "Centrali stoccaggio",
              "fill"
            ).length == 2
              ? this.check_filter(
                  "color",
                  this.filters[0][0],
                  d.properties.descriptio.split(";"),
                  "red",
                  "3",
                  this.centrali_stoc,
                  "Centrali stoccaggio",
                  "fill"
                )[0]
              : this.check_filter(
                  "color",
                  this.filters[0][0],
                  d.properties.descriptio.split(";"),
                  "red",
                  "3",
                  this.centrali_stoc,
                  "Centrali stoccaggio",
                  "fill"
                )
          )
          .attr("stroke-width", 1.5);

        this.point_list.push(
          this.centrali_stoc.map((d, i) => [
            "Centrale stoc",
            d.properties.Name,
            d.properties.descriptio.split(";"),
          ])
        );
      }
      if (this.layers.includes("Centrali termoelettriche")) {
        svg
          .selectAll("centrali_termoelettriche")
          .data(this.centrali_term)
          .enter()
          .append("rect")
          .attr("class", "centrali_termoelettriche")
          .attr("x", (d) => projection(d.geometry.coordinates)[0])
          .attr("y", (d) => projection(d.geometry.coordinates)[1])
          .attr("stroke", (d) =>
            this.check_filter(
              "color",
              this.filters[1][0],
              d.properties.descriptio.split(";"),
              "orange",
              "3",
              this.centrali_term,
              "Centrali termoelettriche",
              "stroke"
            ).length == 2
              ? this.check_filter(
                  "color",
                  this.filters[1][0],
                  d.properties.descriptio.split(";"),
                  "orange",
                  "3",
                  this.centrali_term,
                  "Centrali termoelettriche",
                  "stroke"
                )[0]
              : this.check_filter(
                  "color",
                  this.filters[1][0],
                  d.properties.descriptio.split(";"),
                  "orange",
                  "3",
                  this.centrali_term,
                  "Centrali termoelettriche",
                  "stroke"
                )
          )
          .attr("fill", (d) =>
            this.check_filter(
              "color",
              this.filters[1][0],
              d.properties.descriptio.split(";"),
              "orange",
              "3",
              this.centrali_term,
              "Centrali termoelettriche",
              "fill"
            ).length == 2
              ? this.check_filter(
                  "color",
                  this.filters[1][0],
                  d.properties.descriptio.split(";"),
                  "orange",
                  "3",
                  this.centrali_term,
                  "Centrali termoelettriche",
                  "fill"
                )[0]
              : this.check_filter(
                  "color",
                  this.filters[1][0],
                  d.properties.descriptio.split(";"),
                  "orange",
                  "3",
                  this.centrali_term,
                  "Centrali termoelettriche",
                  "fill"
                )
          )
          .attr("stroke-width", 3)
          .attr("width", (d) =>
            this.check_filter(
              "size",
              this.filters[1][1],
              d.properties.descriptio.split(";"),
              "orange",
              "10",
              this.centrali_term,
              "Centrali termoelettriche"
            ).length == 2
              ? this.check_filter(
                  "size",
                  this.filters[1][1],
                  d.properties.descriptio.split(";"),
                  "orange",
                  "10",
                  this.centrali_term,
                  "Centrali termoelettriche"
                )[1]
              : this.check_filter(
                  "size",
                  this.filters[1][1],
                  d.properties.descriptio.split(";"),
                  "orange",
                  "10",
                  this.centrali_term,
                  "Centrali termoelettriche"
                )
          )
          .attr("height", (d) =>
            this.check_filter(
              "size",
              this.filters[1][1],
              d.properties.descriptio.split(";"),
              "orange",
              "10",
              this.centrali_term,
              "Centrali termoelettriche"
            ).length == 2
              ? this.check_filter(
                  "size",
                  this.filters[1][1],
                  d.properties.descriptio.split(";"),
                  "orange",
                  "10",
                  this.centrali_term,
                  "Centrali termoelettriche"
                )[1]
              : this.check_filter(
                  "size",
                  this.filters[1][1],
                  d.properties.descriptio.split(";"),
                  "orange",
                  "10",
                  this.centrali_term,
                  "Centrali termoelettriche"
                )
          );

        this.point_list.push(
          this.centrali_term.map((d, i) => [
            "Centrale term",
            d.properties.Name,
            d.properties.descriptio.split(";"),
          ])
        );
      }

      //CONTINUARE DA QUI!
      //HTML D:\PISA\Data Journalism\DJ_prova\static\pitesai.png

      //!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
      //AGGIUNGERE FLAG PER OGNI LIVELLO (in modo tale da non ridisegnarlo se non è stato cambiato, ora se si hanno dei livelli già filtrati e se ne aggiunge un altro, tutti vengono ridisegnati (TROPPO TEMPO PER POZZI IDR))
      //SISTEMARE LA QUESTIONE DELLE MULTI LEGENDE CHE SI SOVRAPPONGONO (crea un set di input per display:none/block)
      //AGGIUNGERE "ON HOVER" o/e "ON CLICK" (fra le altre cose, mostrare VALORE REALE DELLA DIMENSIONE (visualmente è log(N)*2 ))
      //!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

      /*
      console.log(
        "descriptions",
        this.centrali_stoc.map((d) => d.properties.descriptio.split(";"))
      );
      console.log(
        "descriptions",
        this.pozzi_stoc.map((d) => d.properties.descriptio.split(";"))
      );
      console.log(
        "descriptions",
        this.pozzi_idrocarburi.map((d) => d.properties.descriptio.split(";"))
      );
      */
      //console.log("FILTRI", this.filters.length, this.filters[0][0].length);

      //flatten list
      this.point_list = this.point_list.flat(1);

      
      //console.log("<POINTS>", this.point_list);
    },
    handle_list(){
     var l = document.getElementById('point_list_col')
     if(this.list_active==true){
        this.list_active=false
        l.style.display='none'
              if (this.zoom == true) {
        this.zoom_in();
      } else if (this.zoom == false) {
        this.zoom_out();
      }
      this.draw_legend();
           var map_cont= document.getElementById("pipelines_map")
        map_cont.style.width='80rem'

      

    }else if(this.list_active==false){
        this.list_active=true

        l.style.display='block'
              if (this.zoom == true) {
        this.zoom_in();
      } else if (this.zoom == false) {
        this.zoom_out();
      }
      this.draw_legend();
           var map_cont= document.getElementById("pipelines_map")
        map_cont.style.width='43rem'

      

    }

    
  }
  },
  watch: {
    filters: function (newVal) {
      //console.log("zoom", this.zoom);
      this.point_list = [];
      this.badge_list = [];
      //console.log("FILT", this.filters);
      if (this.zoom == true) {
        this.zoom_in();
      } else if (this.zoom == false) {
        this.zoom_out();
      }
      this.handle_selection_type();
      this.draw_legend();
    },
    layers: function (newVal) {
      this.point_list = [];
      this.badge_list = [];
      //console.log('LENGHT',this.pozzi_stor_descr.length)
      if (this.pozzi_stor_descr.length == 0) {
        this.pozzi_stor_descr = this.pozzi_storici.map((d) =>
          d.properties.descriptio.split(";")
        );
        this.pozzi_stor_descr = this.pozzi_stor_descr.map((d) =>
          d.map((k) => k.split(":"))
        );
        this.pozzi_stor_descr = this.pozzi_stor_descr.map((d) =>
          d.map((k) => k.map((j) => j.trim()))
        );
        var stor_filters =
          "Operatore,Scopo,Esito,Tipo titolo,Titolo minerario,Provincia/Zona,Terra/Mare,Stato,Anno,Codice"
            .split(",")
            .sort();
        var stor_sizes = "Profondità";
        for (let each of stor_filters) {
          this.pozzi_stor_filtered_all[each] = this.pozzi_stor_descr
            .map((d) => d.filter((k) => k[0] == each))
            .map((j) => j.map((y) => y[1]))
            .flat(1);
          this.stor_filt_sets[each] = [
            ...new Set(this.pozzi_stor_filtered_all[each]),
          ].sort();
          var temp_dict_color_fills = {};
          var temp_dict_color_strokes = {};
          this.stor_filt_sets[each].map(
            (d, i) => (temp_dict_color_fills[d] = colors[i])
          );
          this.stor_filt_sets[each].map(
            (d, i) => (temp_dict_color_strokes[d] = border_colors[i])
          );
          this.pozzi_stor_filtered_all_fills[each] =
            this.pozzi_stor_filtered_all[each].map(
              (d) => temp_dict_color_fills[d]
            );
          this.pozzi_stor_filtered_all_strokes[each] =
            this.pozzi_stor_filtered_all[each].map(
              (d) => temp_dict_color_strokes[d]
            );
        }
      }
      /*console.log(
        "STOR DICTS",
        this.pozzi_stor_filtered_all_fills,
        this.pozzi_stor_filtered_all_strokes
      );*/
      //console.log("done", this.layers);
      this.list_active=false
      this.handle_list()
    },
    active_legend: function (newVal) {
      //console.log("legend", this.active_legend);
      this.handle_selection_type();
      this.draw_legend();
    },
    sel_type: function (newVal) {
      this.handle_selection_type();
      this.draw_legend();
            if (this.zoom == true) {
        this.zoom_in();
      } else if (this.zoom == false) {
        this.zoom_out();
      }
    },
  },
};
</script>

<template id="meta_map">
  <b-container>
  
    <b-row style="background-color:rgba(220,220,220,0.2);border:groove black 2px">
      <code>Legenda</code>

      <b-form-group>
        <b-form-radio-group v-model="active_legend">
          <b-form-radio
            name="Centrali stoccaggio"
            value="Centrali stoccaggio"
            ><a class='figure'>▲</a> Centrali stoccaggio</b-form-radio
          >

          <b-form-radio
            name="Centrali termoelettriche"
            value="Centrali termoelettriche"
            ><a class='figure'>■</a> Centrali termoelettriche</b-form-radio
          >

          <b-form-radio
            name="Pozzi storici"
            value="Pozzi storici"
            ><code class='figure'>⬤</code> Pozzi storici</b-form-radio
          >

          <b-form-radio  name="PITESAI" value="PITESAI"
            >PITESAI</b-form-radio
          >
        </b-form-radio-group>
      </b-form-group>
    </b-row>
    <b-row>
      <b-col cols="6">
<b-button @click="handle_list">{{this.list_active==true ?'Nascondi' :'Mostra'}} lista punti</b-button>
        <div id="pipelines_map_container"></div>
      </b-col>
                      

      <b-col id="point_list_col" cols="6"
        >
        <b-list-group
          id="point_list"
          class="points_list"
          v-for="el in this.point_list"
          :key="el[1]"
        >
          <b-list-group-item
            class="'d-flex justify-content-between align-items-center"
          >
            <b-badge variant="warning" pill>{{ el[0] }}</b-badge>
            <b-badge variant="primary" pill>{{ el[1] }}</b-badge>
            <br /><span v-html="String(String(el[2].map((d,i)=> String(' '+d.split(':')[0].trim().bold()) + ':' + String(d.split(':')[1]).trim())).replaceAll(',','; ').replaceAll('\'',''))"></span></b-list-group-item
          >
        </b-list-group></b-col
      >
    </b-row>
  </b-container>
</template>

<style>
#point_list_col {
  max-height: 31.4rem;
  overflow: scroll;
  -webkit-overflow-scrolling: touch;
  position: absolute;
  z-index: 2;
  margin-left: 45rem;
  margin-top: 2.2rem;
  /*line-break: loose;*/
  white-space:normal;
  color:black;
  width: 30rem;
}
.legend_container {
  max-height: 500px;
  max-width: 200px;
  overflow: scroll;
  -webkit-overflow-scrolling: touch;
  background-color: beige;
  opacity: 0.9;
  position: absolute;
  z-index: 2;
}

#pipelines_map {
  position: absolute;
  background-color: lightblue;
  z-index: 1;
}
#pipelines_map > ellipse {
  position: absolute;
  z-index: 1;
}
.figure{
  color:rgba(231,41,138,0.7);
}


</style>
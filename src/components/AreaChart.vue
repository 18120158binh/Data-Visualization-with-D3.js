<template>
  <div ref="areachart"></div>
</template>

<script>
import * as d3 from "d3";

export default {
  name: "BarChartTest",
  props: {
    data: { required: true },
  },

  created() {
    setTimeout(() => {
      this.createAreaChart(JSON.parse(JSON.stringify(this.data)));
    }, 200);
  },
  methods: {
    createAreaChart(data) {
      // set the dimensions and margins of the graph
      var margin = { top: 20, right: 30, bottom: 30, left: 55 };
      var width = 460 - margin.left - margin.right;
      var height = 400 - margin.top - margin.bottom;
      let checkExist = d3.select(this.$refs.areachart).select("svg");
      if (checkExist) {
        checkExist.remove();
      }
      // append the svg object to the body of the page
      var svg = d3
        .select(this.$refs.areachart)
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
        .append("g")
        .attr("transform", "translate(" + margin.left + "," + margin.top + ")");
      svg
        .append("circle")
        .attr("cx", 20)
        .attr("cy", 8)
        .attr("r", 6)
        .style("fill", "#e41a1c");
      svg
        .append("circle")
        .attr("cx", 140)
        .attr("cy", 8)
        .attr("r", 6)
        .style("fill", "#377eb8");
      svg
        .append("circle")
        .attr("cx", 240)
        .attr("cy", 8)
        .attr("r", 6)
        .style("fill", "#4daf4a");
      svg
        .append("text")
        .attr("x", 30)
        .attr("y", 10)
        .text("Tổng nhập")
        .style("font-size", "15px")
        .attr("alignment-baseline", "middle");
      svg
        .append("text")
        .attr("x", 150)
        .attr("y", 10)
        .text("Tổng bán")
        .style("font-size", "15px")
        .attr("alignment-baseline", "middle");
      svg
        .append("text")
        .attr("x", 250)
        .attr("y", 10)
        .text("Số lượng tồn")
        .style("font-size", "15px")
        .attr("alignment-baseline", "middle");

      // List of groups = header of the csv files
      var keys = ["tongnhap", "tongban", "soluongton"];

      // Add X axis
      var x = d3
        .scalePoint()
        .domain(
          data.map(function (d) {
            return d.tuan;
          })
        )
        .range([0, width]);
      svg
        .append("g")
        .attr("transform", "translate(0," + height + ")")
        .call(d3.axisBottom(x).ticks(5));

      // Add Y axis
      var y = d3.scaleLinear().domain([0, 1000]).range([height, 0]);
      svg.append("g").call(d3.axisLeft(y));

      // color palette
      var color = d3
        .scaleOrdinal()
        .domain(keys)
        .range(["#e41a1c", "#377eb8", "#4daf4a"]);

      //stack the data?
      var stackedData = d3.stack().keys(keys)(data);
      // console.log("This is the stack result: ", stackedData)

      // Show the areas
      svg
        .selectAll("mylayers")
        .data(stackedData)
        .enter()
        .append("path")
        .style("fill", function (d) {
          return color(d.key);
        })
        .attr(
          "d",
          d3
            .area()
            .x(function (d) {
              return x(d.data.tuan);
            })
            .y0(function (d) {
              return y(d[0]);
            })
            .y1(function (d) {
              return y(d[1]);
            })
        );
    },
  },
};
</script>

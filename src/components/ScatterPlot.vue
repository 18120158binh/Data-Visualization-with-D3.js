<template>
  <div ref="scatterplot" :key="data"></div>
</template>

<script>
import * as d3 from "d3";

export default {
  name: "ScatterPlot",
  props: {
    data: { required: true },
  },
  created() {
    setTimeout(() => {
      this.createScatterPlot(JSON.parse(JSON.stringify(this.data)));
    }, 200);
  },
  methods: {
    createScatterPlot(data) {
      // set the dimensions and margins of the graph
      var margin = { top: 10, right: 30, bottom: 30, left: 60 };
      var width = 660 - margin.left - margin.right;
      var height = 400 - margin.top - margin.bottom;

      let checkExist = d3.select(this.$refs.scatterplot).select("svg");
      if (checkExist) {
        checkExist.remove();
      }
      let svg = d3
        .select(this.$refs.scatterplot)
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
        .append("g")
        .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

      // Add X axis
      var x = d3
        .scaleLinear()
        .domain([
          0,
          d3.max(data, function (d) {
            return d.key;
          }),
        ])
        .range([0, width]);
      svg
        .append("g")
        .attr("transform", "translate(0," + height + ")")
        .call(d3.axisBottom(x));

      // Add Y axis
      var y = d3
        .scaleLinear()
        .domain([
          0,
          d3.max(data, function (d) {
            return d.value;
          }),
        ])
        .range([height, 0]);
      svg.append("g").call(d3.axisLeft(y));

      // Add dots
      svg
        .append("g")
        .selectAll("dot")
        .data(data)
        .enter()
        .append("circle")
        .attr("cx", function (d) {
          return x(d.key);
        })
        .attr("cy", function (d) {
          return y(d.value);
        })
        .attr("r", 5)
        .style("fill", "#69b3a2");
    },
  },
};
</script>

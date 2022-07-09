<template>
  <div ref="histogram" :key="data"></div>
</template>

<script>
import * as d3 from "d3";

export default {
  name: "Histogram",
 props: {
    data: { required: true },
  },
  created() {
    setTimeout(() => {
      this.createHistogram(JSON.parse(JSON.stringify(this.data)));
    }, 200);
  },
  methods: {
    createHistogram(data) {
      // console.log(data);
      // set the dimensions and margins of the graph
      var margin = { top: 80, right: 50, bottom: 30, left: 50 },
        width = 1200 - margin.left - margin.right,
        height = 450 - margin.top - margin.bottom;

      let checkExist = d3.select(this.$refs.histogram).select("svg");
      if (checkExist) {
        checkExist.remove();
      }
      let svg = d3
        .select(this.$refs.histogram)
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
      svg
        .append("text")
        .attr("x", width/2 - 130)
        .attr("y", 70)
        .text("Lượng mua hàng phân bổ theo mức giá sản phẩm")
        .attr("class", "title");
      svg= svg.append("g")
        .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

      // X axis: scale and draw:
      var x = d3
        .scaleLinear()
        .domain([0, d3.max(data, function(d) { return +d.key })]) // can use this instead of 1000 to have the max of data: d3.max(data, function(d) { return +d.price })
        .range([0, width]);
      svg
        .append("g")
        .attr("stroke", "black")
        .attr("transform", "translate(0," + height + ")")
        .call(d3.axisBottom(x))
        .append("text")
        .attr("transform", "rotate(0)")
        .attr("y", -256)
        .attr("dy", "-10.1em")
        .attr("text-anchor", "end")
        .attr("stroke", "black")
        .text("Lượng bán");

      // set the parameters for the histogram
      var histogram = d3
        .histogram()
        .value(function (d) {
          return d.key;
        }) // I need to give the vector of value
        .domain(x.domain()) // then the domain of the graphic
        .thresholds(x.ticks(70)); // then the numbers of bins
      
      // And apply this function to data to get the bins
      var bins = histogram(data);
      // console.log(bins);

      // Y axis: scale and draw:
      var y = d3.scaleLinear().range([height, 0]);
      y.domain([
        0,
        // d3.max(data, function (d) {
        //   return d.value;
        // }),
        40000
      ]); // d3.hist has to be called before the Y axis obviously
      svg.append("g").attr("stroke", "black").call(d3.axisLeft(y))
        .append("text")
        .attr("y", height + 5)
        .attr("x", width + 44)
        .attr("text-anchor", "end")
        .attr("stroke", "black")
        .text("Mức giá");

      // Add a tooltip div. Here I define the general feature of the tooltip: stuff that do not depend on the data point.
      // Its opacity is set to 0: we don't see it by default.
      var tooltip = d3
        .select(this.$refs.histogram)
        .append("div")
        .style("opacity", 0)
        .attr("class", "tooltip")
        .style("background-color", "black")
        .style("color", "white")
        .style("border-radius", "5px")
        .style("padding", "10px");

      // A function that change this tooltip when the user hover a point.
      // Its opacity is set to 1: we can now see it. Plus it set the text and position of tooltip depending on the datapoint (d)
      var showTooltip = function (event, d) {
        console.log(d);
        tooltip.transition().duration(100).style("opacity", 1);
        tooltip
          .html("Range: " + d.x0 + " - " + d.x1)
      };
      var moveTooltip = function (event, d) {
        tooltip
          .style("left", d3.pointer(event)[0] + 90 + "px")
          .style("top", d3.pointer(event)[1] + 500 + "px");
      };
      // A function that change this tooltip when the leaves a point: just need to set opacity to 0 again
      var hideTooltip = function (d) {
        tooltip.transition().duration(100).style("opacity", 0);
      };

      // append the bar rectangles to the svg element
      svg
        .selectAll("rect")
        .data(bins)
        .enter()
        .append("rect")
        .attr("x", 1)
        .attr("transform", function (d) {
          var length = 0
          d.map(function(tmp){
            length += tmp.value
            }
            )
          return "translate(" + x(d.x0) + "," + y(length) + ")";
        })
        .attr("width", function (d) {
          return x(d.x1) - x(d.x0) - 1;
        })
        .attr("height", function (d) {
          var length = 0
          d.map(function(tmp){
            length += tmp.value
            }
            )
          return height - y(length);
        })
        .style("fill", "#69b3a2")
        // Show tooltip on hover
        .on("mouseover", showTooltip)
        .on("mousemove", moveTooltip)
        .on("mouseleave", hideTooltip);
    },
  },
};
</script>

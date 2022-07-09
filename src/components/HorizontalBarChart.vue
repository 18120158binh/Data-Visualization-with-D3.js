<template>
  <div ref="horizontalbarchart"></div>
</template>

<script>
import * as d3 from "d3";

export default {
  name: "HorizontalBarChart",
  props: {
    data: { required: true },
  },

  created() {
    setTimeout(() => {
      this.createHorizontalBarChart(JSON.parse(JSON.stringify(this.data)));
    }, 200);
  },
  methods: {
    createHorizontalBarChart(data) {
      // set the dimensions and margins of the graph
      var margin = { top: 40, right: 10, bottom: 40, left: 150 },
        width = 600 - margin.left - margin.right,
        height = 400 - margin.top - margin.bottom;

      let checkExist = d3.select(this.$refs.horizontalbarchart).select("svg");
      if (checkExist) {
        checkExist.remove();
      }
      let svg = d3
        .select(this.$refs.horizontalbarchart)
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
        .append("g")
        .attr("transform", "translate(" + margin.left + "," + margin.top + ")");
      
      svg
        .append("g")
        .attr("transform", "translate(" + width / 3 + "," + -20 + ")")
        .append("text")
        .text("10 sản phẩm bán chạy")
        .attr("class", "title");

      // Add X axis
      var x = d3
        .scaleLinear()
        .domain([
          0,
          d3.max(data, function (d) {
            return d.value;
          }),
        ])
        .range([0, width]);
      svg
        .append("g")
        .attr("transform", "translate(0," + height + ")")
        .call(d3.axisBottom(x))
        .selectAll("text")
        .attr("transform", "translate(-10,0)rotate(-45)")
        .attr("stroke", "black")
        .style("text-anchor", "end");

      // Y axis
      var y = d3
        .scaleBand()
        .range([0, height])
        .domain(
          data.map(function (d) {
            return d.key;
          })
        )
        .padding(0.2);
      svg.append("g").attr("stroke", "black").call(d3.axisLeft(y));

      var tooltip = d3
        .select(this.$refs.horizontalbarchart)
        .append("div")
        .style("opacity", 0)
        .attr("class", "tooltip")
        .style("background-color", "white")
        .style("border", "solid")
        .style("border-width", "1px")
        .style("border-radius", "5px")
        .style("padding", "10px");

      // Three function that change the tooltip when user hover / move / leave a cell
      var mouseover = function (event, d) {
        console.log(d);
        tooltip.transition().duration(100).style("opacity", 1);
        tooltip
          .html(
            'Doanh số bán: ' + d.data.value
          )
      };
      var mousemove = function (event, d) {
        tooltip
          .style("left", d3.pointer(event)[0] + 300 + "px") // It is important to put the +90: other wise the tooltip is exactly where the point is an it creates a weird effect
          .style("top", d3.pointer(event)[1] + 100 + "px");
      };
      var mouseleave = function (d) {
        tooltip.transition().duration(100).style("opacity", 0);
      };

      //Bars
      svg
        .selectAll("myRect")
        .data(data)
        .enter()
        .append("rect")
        .attr("x", x(0))
        .attr("y", function (d) {
          return y(d.key);
        })
        .attr("width", function (d) {
          return x(d.value);
        })
        .attr("height", y.bandwidth())
        .attr("fill", "#69b3a2");
      append("text")
        .attr("transform", function (d) {
          return "translate(" + label.centroid(d) + ")";
        })
        .attr("stroke", "black")
        .attr("text-anchor", "middle")
        .attr("x", "60")
        .attr("y", "75")
        .text(function (d) {
          return +d.data.key;
        });
      svg
        .on("mouseover", mouseover)
        .on("mousemove", mousemove)
        .on("mouseleave", mouseleave);
    },
  },
};
</script>

<template>
  <div ref="stackedbarchart" :key="data"></div>
</template>

<script>
import * as d3 from "d3";

export default {
  name: "StackedBarChart",
  props: {
    data: { required: true },
  },

  created() {
    setTimeout(() => {
      this.createStackedBar(JSON.parse(JSON.stringify(this.data)));
    }, 200);
  },
  methods: {
    createStackedBar(data) {
      // List of subgroups = header of the csv files = soil condition here
      var subgroups = ["dagiao", "hoantra", "dahuy"];
      var stackedcolor = [];
      for (let i = 0; i < subgroups.length; i++) {
        stackedcolor.push(d3.schemeSet2[i]);
      }
      // set the dimensions and margins of the graph
      var margin = { top: 50, right: 30, bottom: 20, left: 50 },
        width = 550 - margin.left - margin.right,
        height = 680 - margin.top - margin.bottom;

      let checkExist = d3.select(this.$refs.stackedbarchart).select("svg");
      if (checkExist) {
        checkExist.remove();
      }
      let svg = d3
        .select(this.$refs.stackedbarchart)
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
      svg
        .append("text")
        .attr("x", 100)
        .attr("y", 20)
        .text("Tổng lượng đơn theo tình trạng đơn hàng")
        .attr("class", "title");
      svg
        .append("circle")
        .attr("cx", 130)
        .attr("cy", 40)
        .attr("r", 6)
        .style("fill", stackedcolor[0]);
      svg
        .append("circle")
        .attr("cx", 230)
        .attr("cy", 40)
        .attr("r", 6)
        .style("fill", stackedcolor[1]);
      svg
        .append("text")
        .attr("x", 140)
        .attr("y", 40)
        .text("Đã giao")
        .style("font-size", "15px")
        .attr("alignment-baseline", "middle");
      svg
        .append("text")
        .attr("x", 240)
        .attr("y", 40)
        .text("Hoàn trả")
        .style("font-size", "15px")
        .attr("alignment-baseline", "middle");
      svg
        .append("circle")
        .attr("cx", 330)
        .attr("cy", 40)
        .attr("r", 6)
        .style("fill", stackedcolor[2]);
      svg
        .append("text")
        .attr("x", 340)
        .attr("y", 40)
        .text("Đã hủy")
        .style("font-size", "15px")
        .attr("alignment-baseline", "middle");

      svg = svg.append("g")
        .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

      // List of groups = species here = value of the first column called group -> I show them on the X axis
      var groups = d3.map(data, function (d) {
        return d.key;
      });

      // Add X axis
      var x = d3.scaleBand().domain(groups).range([0, width]).padding([0.2]);
      svg
        .append("g")
        .attr("transform", "translate(0," + height + ")")
        .attr("stroke", "black")
        .call(d3.axisBottom(x).tickSizeOuter(0))
        .append("text")
        .attr("transform", "rotate(0)")
        .attr("y", -526)
        .attr("dy", "-10.1em")
        .attr("text-anchor", "end")
        .attr("stroke", "black")
        .text("Tổng đơn");

      // Add Y axis
      var y = d3.scaleLinear().domain([0, 1400]).range([height, 0]);
      svg.append("g").attr("stroke", "black").call(d3.axisLeft(y));

      // color palette = one color per subgroup
      var color = d3.scaleOrdinal().domain(subgroups).range(stackedcolor);

      //stack the data? --> stack per subgroup
      var stackedData = d3.stack().keys(subgroups)(data);

      // ----------------
      // Create a tooltip
      // ----------------
      var tooltip = d3
        .select(this.$refs.stackedbarchart)
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
        var subgroupName = d3.select(this.parentNode).datum().key;
        var subgroupValue = d.data[subgroupName];
        tooltip.transition().duration(100).style("opacity", 1);
        tooltip
          .html(
            "Đã giao: " + d.data.dagiao + "<br>" + "Đã hủy: " + d.data.dahuy + "<br>" + "Hoàn trả: " + d.data.hoantra 
          )
      };
      var mousemove = function (event, d) {
        tooltip
          .style("left", d3.pointer(event)[0] + 90 + "px") // It is important to put the +90: other wise the tooltip is exactly where the point is an it creates a weird effect
          .style("top", d3.pointer(event)[1] + "px");
      };
      var mouseleave = function (d) {
        tooltip.transition().duration(100).style("opacity", 0);
      };

      // Show the bars
      svg
        .append("g")
        .selectAll("g")
        // Enter in the stack data = loop key per key = group per group
        .data(stackedData)
        .enter()
        .append("g")
        .attr("fill", function (d) {
          return color(d.key);
        })
        .selectAll("rect")
        // enter a second time = loop subgroup per subgroup to add all rectangles
        .data(function (d) {
          return d;
        })
        .enter()
        .append("rect")
        .attr("x", function (d) {
          return x(d.data.key);
        })
        .attr("y", function (d) {
          return y(d[1]);
        })
        .attr("height", function (d) {
          return y(d[0]) - y(d[1]);
        })
        .attr("width", x.bandwidth())
        .attr("stroke", "grey")
        .on("mouseover", mouseover)
        .on("mousemove", mousemove)
        .on("mouseleave", mouseleave);
    },
  },
};
</script>

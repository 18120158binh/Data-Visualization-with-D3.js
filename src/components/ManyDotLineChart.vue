<template>
  <div ref="manydotlinechart"></div>
</template>

<script>
import * as d3 from "d3";

export default {
  name: "ManyDotLineChart",
  props: {
    data: { required: true },
  },
  created() {
    setTimeout(() => {
      this.createDotLineChartwithMany(JSON.parse(JSON.stringify(this.data)));
    }, 200);
  },
  methods: {
    createDotLineChartwithMany(data) {
      let color1 = d3.schemeSet2[0];
      let color2 = d3.schemeSet2[1];
      // set the dimensions and margins of the graph
      var margin = { top: 20, right: 100, bottom: 30, left: 50 },
        width = 1200 - margin.left - margin.right,
        height = 400 - margin.top - margin.bottom;

      let checkExist = d3.select(this.$refs.manydotlinechart).select("svg");
      if (checkExist) {
        checkExist.remove();
      }
      let svg = d3
        .select(this.$refs.manydotlinechart)
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)

      svg
        .append("circle")
        .attr("cx", 460)
        .attr("cy", 6)
        .attr("r", 6)
        .style("fill", color1);
      svg
        .append("circle")
        .attr("cx", 640)
        .attr("cy", 6)
        .attr("r", 6)
        .style("fill", color2);
      svg
        .append("text")
        .attr("x", 480)
        .attr("y", 10)
        .text("Lượng nhập")
        .style("font-size", "15px")
        .attr("alignment-baseline", "middle");
      svg
        .append("text")
        .attr("x", 660)
        .attr("y", 10)
        .text("Lượng bán")
        .style("font-size", "15px")
        .attr("alignment-baseline", "middle");
      //Read the data
      svg = svg.append("g")
        .attr("transform", "translate(" + margin.left + "," + margin.top + ")");
      // List of groups (here I have one group per column)
      var allGroup = ["luongnhap", "luongban"];

      // Reformat the data: we need an array of arrays of {x, y} tuples
      var dataReady = allGroup.map(function (grpName) {
        // .map allows to do something for each element of the list
        return {
          name: grpName,
          values: data.map(function (d) {
            return { key: d.key, value: +d[grpName] };
          }),
        };
      });

      // A color scale: one color for each group
      var myColor = d3
        .scaleOrdinal()
        .domain(allGroup)
        .range([color1, color2]);

      // Add X axis --> it is a date format
      var x = d3
        .scalePoint()
        .domain(
          data.map(function (d) {
            return d.key;
          })
        )
        .range([0, width]);
      svg
        .append("g")
        .attr("transform", "translate(0," + height + ")")
        .attr("stroke", "black")
        .call(d3.axisBottom(x))
        .attr("stroke", "black")
        .append("text")
        .attr("transform", "rotate(0)")
        .attr("y", -260)
        .attr("dy", "-10.1em")
        .attr("text-anchor", "end")
        .attr("stroke", "black")
        .text("Số lượng");

      // Add Y axis
      var y = d3.scaleLinear().domain([0, 18000]).range([height, 0]);
      svg.append("g").attr("stroke", "black").call(d3.axisLeft(y))
        .append("text")
        .attr("y", height + 5)
        .attr("x", width + 40)
        .attr("text-anchor", "end")
        .attr("stroke", "black")
        .text("Ngày");

      // Add the lines
      var line = d3
        .line()
        .x(function (d) {
          return x(d.key);
        })
        .y(function (d) {
          return y(d.value);
        });
      svg
        .selectAll("myLines")
        .data(dataReady)
        .enter()
        .append("path")
        .attr("d", function (d) {
          return line(d.values);
        })
        .attr("stroke", function (d) {
          return myColor(d.name);
        })
        .style("stroke-width", 4)
        .style("fill", "none");

      // Three function that change the tooltip when user hover / move / leave a cell
      var mouseover = function (d) {
        Tooltip.style("opacity", 1);
      };
      var mousemove = function (event, d) {
        Tooltip.html("Exact value: " + d.soluongton)
          .style("left", d3.pointer(event)[0] + 170 + "px")
          .style("top", d3.pointer(event)[1] + "px");
      };
      var mouseleave = function (d) {
        Tooltip.style("opacity", 0);
      };

      // Add the points
      svg
        // First we need to enter in a group
        .selectAll("myDots")
        .data(dataReady)
        .enter()
        .append("g")
        .style("fill", function (d) {
          return myColor(d.name);
        })
        // Second we need to enter in the 'values' part of this group
        .selectAll("myPoints")
        .data(function (d) {
          return d.values;
        })
        .enter()
        .append("circle")
        .attr("cx", function (d) {
          return x(d.key);
        })
        .attr("cy", function (d) {
          return y(d.value);
        })
        .attr("r", 5)
        .attr("stroke", "white")
        .on("mouseover", mouseover)
        .on("mousemove", mousemove)
        .on("mouseleave", mouseleave);

      // Add a legend at the end of each line
      svg
        .selectAll("myLabels")
        .data(dataReady)
        .enter()
        .append("g")
        .append("text")
        .datum(function (d) {
          return { name: d.name, value: d.values[d.values.length - 1] };
        }) // keep only the last value of each key series
        .attr("transform", function (d) {
          return "translate(" + x(d.value.key) + "," + y(d.value.value) + ")";
        }); // Put the text at the position of the last point
      // .attr("x", 12) // shift the text a bit more right
      // .text(function (d) {
      //   return d.name;
      // })
      // .style("fill", function (d) {
      //   return myColor(d.name);
      // })
      // .style("font-size", 15);
    },
  },
};
</script>
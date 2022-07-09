<template>
  <div ref="groupedbarplot"></div>
</template>

<script>
import * as d3 from "d3";

export default {
  name: "GroupedBarplot",
  props: {
    data: { required: true },
    widthChart: { required: false },
    heightChart: { required: false },
    maxValue: { required: false },
  },
  created() {
    setTimeout(() => {
      this.createGroupedBarplot(JSON.parse(JSON.stringify(this.data)));
    }, 200);
  },
  methods: {
    createGroupedBarplot(data) {
      // set the dimensions and margins of the graph
      var margin = { top: 10, right: 30, bottom: 20, left: 250 },
        width = parseInt(this.widthChart) - margin.left - margin.right,
        height = parseInt(this.heightChart) - margin.top - margin.bottom;

      let checkExist = d3.select(this.$refs.groupedbarplot).select("svg");
      if (checkExist) {
        checkExist.remove();
      }

      // List of subgroups = header of the csv files = soil condition here
      var subgroups = data[0].value.map(function (d) {
        return d.key;
      });
      var arrcolor = [];
      for (let i = 0; i < subgroups.length; i++) {
        arrcolor.push(d3.schemeSet2[i]);
      }

      // append the svg object to the body of the page
      var svg = d3
        .select(this.$refs.groupedbarplot)
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
      
      let yy = 60;
      let cy = 60;
      for (let i = 0; i < subgroups.length; i++) {
        svg
          .append("circle")
          .attr("cx", 10)
          .attr("cy", cy)
          .attr("r", 8)
          .style("fill", arrcolor[i]);
        svg
          .append("text")
          .attr("x", 20)
          .attr("y", yy)
          .text(subgroups[i])
          .style("font-size", "12px")
          .attr("alignment-baseline", "middle");
        yy += 25;
        cy += 25;
      }
      
      svg = svg.append("g")
        .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

      // List of groups = species here = value of the first column called group -> I show them on the X axis
      var groups = data.map(function (d) {
        return d.key;
      });
      // Add X axis
      var x = d3.scaleBand().domain(groups).range([0, width]).padding([0.2]);
      svg
        .append("g")
        .attr("transform", "translate(0," + height + ")")
        .call(d3.axisBottom(x).tickSize(0));

      // Add Y axis
      var y = d3.scaleLinear().domain([0, parseInt(this.maxValue)]).range([height, 0]);
      svg.append("g").call(d3.axisLeft(y));

      // Another scale for subgroup position?
      var xSubgroup = d3
        .scaleBand()
        .domain(subgroups)
        .range([0, x.bandwidth()])
        .padding([0.05]);
      
      // color palette = one color per subgroup
      var color = d3
        .scaleOrdinal()
        .domain(subgroups)
        .range(arrcolor);

      // Show the bars
      svg
        .append("g")
        .selectAll("g")
        // Enter in data = loop group per group
        .data(data)
        .enter()
        .append("g")
        .attr("transform", function (d) {
          return "translate(" + x(d.key) + ",0)";
        })
        .selectAll("rect")
        .data(function (d) {
          //   return subgroups.map(function (key) {
          //     return { key: key, value: d.value };
          //   });
          return d.value.map(function (key) {
            return { key: key.key, value: key.value };
          });
        })
        .enter()
        .append("rect")
        .attr("x", function (d) {
          return xSubgroup(d.key);
        })
        .attr("y", function (d) {
          return y(d.value);
        })
        .attr("width", xSubgroup.bandwidth())
        .attr("height", function (d) {
          return height - y(d.value);
        })
        .attr("fill", function (d) {
          return color(d.key);
        });
    },
  },
};
</script>

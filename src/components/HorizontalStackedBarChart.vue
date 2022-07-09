<template>
  <div ref="horizontalstackedbarchart" :key="data">
  </div>
</template>

<script>
import * as d3 from "d3";

export default {
  name: "HorizontalStackedBarChart",
  props: {
    data: { required: true },
  },
  created() {
    setTimeout(() => {
      this.createHorizontalStackedBar(JSON.parse(JSON.stringify(this.data)));
    }, 200);
  },
  methods: {
    createHorizontalStackedBar(data) {
      // set the dimensions and margins of the graph
      var margin = { top: 40, right: 50, bottom: 20, left: 150 },
        width = 850 - margin.left - margin.right,
        height = 400 - margin.top - margin.bottom;

      let checkExist = d3.select(this.$refs.horizontalstackedbarchart).select("svg");
      if (checkExist) {
        checkExist.remove();
      }
      var svg = d3
        .select(this.$refs.horizontalstackedbarchart)
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
      svg
        .append("text")
        .attr("x", width/2 - 130)
        .attr("y", 20)
        .text("Doanh số mua theo nhóm tuổi khách hàng trong từng danh mục")
        .attr("class", "title");

      svg = svg.append("g")
        .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

      var y = d3.scaleBand()			// x = d3.scaleBand()	
          .rangeRound([0, height])	// .rangeRound([0, width])
          .paddingInner(0.05)
          .align(0.1);

      var x = d3.scaleLinear()		// y = d3.scaleLinear()
          .rangeRound([0, width]);	// .rangeRound([height, 0]);
      
      var z = d3.scaleOrdinal()
          .range(d3.schemeCategory10);

      var subgroups = ['Age <= 18', 'Age 19 - 30', 'Age 31 - 45', 'Age 46 - 60', 'Age > 60']

      var groups = data.map(function(d) {
        return d.key;
      });

        // data.sort(function(a, b) { return b.total - a.total; });
      y.domain(groups);					// x.domain...
      x.domain([0, 4500000]).nice();	// y.domain...
      z.domain(subgroups);

      //stack the data? --> stack per subgroup
      var stackedData = d3.stack().keys(subgroups)(data);

      svg.append("g")
            .attr("class", "axis")
            .attr("stroke", "black")
            .attr("transform", "translate(0,0)") 						//  .attr("transform", "translate(0," + height + ")")
            .call(d3.axisLeft(y));									//   .call(d3.axisBottom(x));

      svg
        .append("g")
        .attr("transform", "translate(0," + height + ")")
        .attr("stroke", "black")
        .call(d3.axisBottom(x).tickSizeOuter(0))
        .append("text")
        .attr("y", height - 340)
        .attr("x", width + 50)
        .attr("text-anchor", "end")
        .attr("stroke", "black")
        .text("Doanh số");

      var tooltip = d3
        .select(this.$refs.horizontalstackedbarchart)
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
        let tooltiptext = '';
        for(let i = 0; i < subgroups.length; i++){
          tooltiptext = tooltiptext + subgroups[i] + ': ' + (d.data)[subgroups[i]] + '</br>'
        }
        tooltip.transition().duration(100).style("opacity", 1);
        tooltip
          .html(
            tooltiptext
          )
      };
      var mousemove = function (event, d) {
        tooltip
          .style("left", d3.pointer(event)[0] + 600 + "px") // It is important to put the +90: other wise the tooltip is exactly where the point is an it creates a weird effect
          .style("top", d3.pointer(event)[1] + 100 + "px");
      };
      var mouseleave = function (d) {
        tooltip.transition().duration(100).style("opacity", 0);
      };

      svg.append("g")
          .selectAll("g")
          .data(stackedData)
          .enter().append("g")
          .attr("fill", function(d) { return z(d.key); })
          .selectAll("rect")
          .data(function(d) { return d; })
          .enter().append("rect")
          .attr("y", function(d) { return y(d.data.key); })	    //.attr("x", function(d) { return x(d.data.State); })
          .attr("x", function(d) { return x(d[0]); })			    //.attr("y", function(d) { return y(d[1]); })	
          .attr("width", function(d) { return x(d[1]) - x(d[0]); })	//.attr("height", function(d) { return y(d[0]) - y(d[1]); })
          .attr("height", y.bandwidth())
          .on("mouseover", mouseover)
          .on("mousemove", mousemove)
          .on("mouseleave", mouseleave);					  

        var legend = svg.append("g")
            .attr("font-family", "sans-serif")
            .attr("font-size", 10)
            .attr("stroke", "black")
            .attr("text-anchor", "end")
          .selectAll("g")
          .data(subgroups.slice().reverse())
          .enter().append("g")
          //.attr("transform", function(d, i) { return "translate(0," + i * 20 + ")"; });
        .attr("transform", function(d, i) { return "translate(50," + (0 + i * 20) + ")"; });

        legend.append("rect")
            .attr("x", width - 19)
            .attr("width", 19)
            .attr("height", 19)
            .attr("fill", z);

        legend.append("text")
            .attr("x", width - 24)
            .attr("y", 9.5)
            .attr("dy", "0.32em")
            .text(function(d) { return d; });
    },
  },
};
</script>

<template>
  <div ref="barchart2yaxis"></div>
</template>

<script>
import * as d3 from "d3";

export default {
  name: "BarChartTwoYAxis",
  props: {
    data1: { required: true },
    data2: { required: true },
  },

  created() {
    setTimeout(() => {
      this.createTwoYAxisChart(JSON.parse(JSON.stringify(this.data1)), JSON.parse(JSON.stringify(this.data2)))
    }, 200);
  },
  methods: {
     createBarChartWithAnimated() {
      let checkExist = d3.select(this.$refs.barchart).select("svg");
      if (checkExist) {
        checkExist.remove();
      }
      let svg = d3
        .select(this.$refs.barchart)
        .append("svg")
        .attr("width", 500)
        .attr("height", 350);
      var margin = 200;
      var width = svg.attr("width") - margin;
      var height = svg.attr("height") - margin;

      // svg
      //   .append("text")
      //   .attr("transform", "translate(100,0)")
      //   .attr("x", 50)
      //   .attr("y", 50)
      //   .attr("font-size", "24px")
      //   .text("Tong doanh thu");

      var x = d3.scaleBand().range([0, width]).padding(0.4),
        y = d3.scaleLinear().range([height, 0]);

      var g = svg
        .append("g")
        .attr("transform", "translate(" + 100 + "," + 100 + ")");
      let data = JSON.parse(JSON.stringify(this.data));
      x.domain(
        data.map(function (d) {
          return d.key;
        })
      );
      y.domain([
        0,
        d3.max(data, function (d) {
          return d.value;
        }),
      ]);

      g.append("g")
        .attr("transform", "translate(0," + height + ")")
        .call(d3.axisBottom(x))
        .append("text")
        .attr("y", height - 130)
        .attr("x", width + 10)
        .attr("text-anchor", "end")
        .attr("stroke", "black")
        .text("Tháng");

      g.append("g")
        .call(
          d3
            .axisLeft(y)
            .tickFormat(function (d) {
              return "$" + d;
            })
            .ticks(5)
        )
        .append("text")
        .attr("transform", "rotate(0)")
        .attr("y", 86)
        .attr("dy", "-10.1em")
        .attr("text-anchor", "end")
        .attr("stroke", "black")
        .text("Tong doanh thu");

      g.selectAll(".bar")
        .data(data)
        .enter()
        .append("rect")
        .attr("class", "bar")
        .on("mouseover", function (event, d) {
          d3.select(this).attr("class", "highlight");
          d3.select(this)
            .transition() // adds animation
            .duration(400)
            .attr("width", x.bandwidth())
            .attr("y", function (d) {
              return y(d.value);
            })
            .attr("height", function (d) {
              return height - y(d.value);
            });
          g.append('line')
            .attr('class', 'linemark')
            .attr('x1', 0)
            .attr('y1',function () {
              return y(d.value);
            })
            .attr('x2', width)
            .attr('y2', function () {
              return y(d.value);
            })
            .attr('stroke', 'red')

          g.append("text")
            .attr("class", "val")
            .attr("x", function () {
              return x(d.key);
            })
            .attr("y", function () {
              return y(d.value);
            })
            .text(function () {
              return ["$" + d.value]; // value of the text
            });
        }) //Add listener for the mouseover event
        .on("mouseout", function () {
          // use the text label class to remove label on mouseout
          d3.select(this).attr("class", "bar");
          d3.select(this)
            .transition() // adds animation
            .duration(400)
            .attr("width", x.bandwidth())
            .attr("y", function (d) {
              return y(d.value);
            })
            .attr("height", function (d) {
              return height - y(d.value);
            });

          d3.selectAll(".val").remove();
           d3.selectAll(".linemark").remove();
        }) //Add listener for the mouseout event
        .attr("x", function (d) {
          return x(d.key);
        })
        .attr("y", function (d) {
          return y(d.value);
        })
        .attr("width", x.bandwidth())
        .transition()
        .ease(d3.easeLinear)
        .duration(400)
        .delay(function (d, i) {
          return i * 50;
        })
        .attr("height", function (d) {
          return height - y(d.value);
        });
    },
    createTwoYAxisChart(dataY1, dataY2) {
      let checkExist = d3.select(this.$refs.barchart2yaxis).select("svg");
      if (checkExist) {
        checkExist.remove();
      }
      let svg = d3
        .select(this.$refs.barchart2yaxis)
        .append("svg")
        .attr("width", 1000)
        .attr("height", 350);
      var width = svg.attr("width") - 200;
      var height = svg.attr("height") - 150;
      var xScale = d3.scaleBand().range([0, width]).padding(0.4);
      var yScale = d3.scaleLinear().range([height, 0]);

      var g = svg
        .append("g")
        .attr("transform", "translate(" + 100 + "," + 100 + ")");
      xScale.domain(
        dataY1.map(function (d) {
          return d.key;
        })
      );
      yScale.domain([
        0,
        d3.max(dataY1, function (d) {
          return d.value;
        }),
      ]);

      var tooltip = d3
        .select(this.$refs.barchart2yaxis)
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
        var doanhso;
        dataY2.map(function(d2) {
          if(d2.key == d.key){
            doanhso = d2.value
          }
        })
        tooltip.transition().duration(100).style("opacity", 1);
        tooltip
          .html(
            'Doanh thu: ' + d.value + '</br>' + 'Doanh số: ' + doanhso
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

      g.append("g")
        .attr("transform", "translate(0," + height + ")")
        .attr("stroke", "black")
        .call(d3.axisBottom(xScale));
      g.append("g")
        .call(
          d3
            .axisLeft(yScale)
            .tickFormat(function (d) {
              return d + " VND";
            })
            .ticks(6) //so luong moc tren truc y
        )
        .attr("stroke", "black")
        .append("text")
        .attr("transform", "rotate(0)")
        .attr("y", 100)
        .attr("dy", "-10.1em")
        .attr("text-anchor", "end")
        .attr("font-size", "12px")
        .text("Doanh thu");

      g.append("g")
        .attr("transform", "translate(0," + height + ")")
        .call(d3.axisBottom(xScale))
        .append("text")
        .attr("y", height - 160)
        .attr("x", width / 2)
        .attr("text-anchor", "end")
        .attr("stroke", "black")
        .text("Thời gian");
      g.selectAll(".bar")
        .data(dataY1)
        .enter()
        .append("rect")
        .attr("class", "bar")
        .attr("x", function (d) {
          return xScale(d.key);
        })
        .attr("y", function (d) {
          return yScale(d.value);
        })
        .attr("width", xScale.bandwidth())
        .attr("height", function (d) {
          return height - yScale(d.value);
        })
        .on("mouseover", mouseover)
        .on("mousemove", mousemove)
        .on("mouseleave", mouseleave);

      //line chart

      var g2 = svg
        .append("g")
        .attr("transform", "translate(" + 900 + "," + 100 + ")");
      yScale.domain([
        0,
        d3.max(dataY2, function (d) {
          return d.value;
        }),
      ]);

      g2.append("g")
        .attr("transform", "translate(-800," + height + ")")
        .call(d3.axisBottom(xScale));
      g2.append("g").call(
        d3
          .axisRight(yScale)
          .tickFormat(function (d) {
            return d + "";
          })
          .ticks(6) //so luong moc tren truc y
      )
        .attr("stroke", "black")
        .append("text")
        .attr("transform", "rotate(0)")
        .attr("y", 100)
        .attr("dy", "-10.1em")
        .attr("text-anchor", "end")
        .attr("font-size", "12px")
        .text("Doanh số");

      // Add the points
      g.append("g")
        .selectAll("dot")
        .data(dataY2)
        .enter()
        .append("circle")
        .attr("cx", function (d) {
          return xScale(d.key) + xScale.bandwidth() / 2;
        })
        .attr("cy", function (d) {
          return yScale(d.value);
        })
        .attr("r", 5)
        .attr("fill", "#69b3a2");
      g.append("g")
        .append("path")
        .datum(dataY2)
        .attr("fill", "none")
        .attr("stroke", "#69b3a2")
        .attr("stroke-width", 3)
        .attr(
          "d",
          d3
            .line()
            .x(function (d) {
              return xScale(d.key) + xScale.bandwidth() / 2;
            })
            .y(function (d) {
              return yScale(d.value);
            })
        );
    },

  },
};
</script>

<style>
line {
  stroke: gray;
}

line#limit {
  stroke: #FED966;
  stroke-width: 3;
  stroke-dasharray: 3 6;
}
</style>
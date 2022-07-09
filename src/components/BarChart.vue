<template>
  <div ref="barchart" :key="data"></div>
</template>

<script>
import * as d3 from "d3";

export default {
  name: "BarChart",
  props: {
    data: { required: true },
  },

  created() {
    setTimeout(() => {
      // this.createBarChart(JSON.parse(JSON.stringify(this.data)));
      this.createBarChartWithAnimated();
    }, 200);
  },
  methods: {
    createBarChart(data) {
      let checkExist = d3.select(this.$refs.barchart).select("svg");
      if (checkExist) {
        checkExist.remove();
      }
      let svg = d3
        .select(this.$refs.barchart)
        .append("svg")
        .attr("width", 600)
        .attr("height", 350);
      console.log(svg);
      var width = svg.attr("width") - 200;
      var height = svg.attr("height") - 200;
      var xScale = d3.scaleBand().range([0, width]).padding(0.4);
      var yScale = d3.scaleLinear().range([height, 0]);

      var g = svg
        .append("g")
        .attr("transform", "translate(" + 100 + "," + 100 + ")");
      xScale.domain(
        data.map(function (d) {
          return d.key;
        })
      );
      yScale.domain([
        0,
        d3.max(data, function (d) {
          return d.value;
        }),
      ]);
      g.append("g")
        .attr("transform", "translate(0," + height + ")")
        .call(d3.axisBottom(xScale));
      g.append("g")
        .call(
          d3
            .axisLeft(yScale)
            .tickFormat(function (d) {
              return d + " VND";
            })
            .ticks(4) //so luong moc tren truc y
        )
        .append("text")
        .attr("transform", "rotate(0)")
        .attr("y", 80)
        .attr("dy", "-10.1em")
        .attr("text-anchor", "end")
        .attr("stroke", "black")
        .text("Revenue");

      g.append("g")
        .attr("transform", "translate(0," + height + ")")
        .call(d3.axisBottom(xScale))
        .append("text")
        .attr("y", height - 130)
        .attr("x", width + 20)
        .attr("text-anchor", "end")
        .attr("stroke", "black")
        .text("Month");
      g.selectAll(".bar")
        .data(data)
        .enter()
        .append("rect")
        .attr("class", "bar")
        .on("mouseover", function (d) {
          d3.select(this).attr("class", "highlight");
          d3.select(this)
            .transition() // adds animation
            .duration(400)
            .attr("width", x.bandwidth())
            .attr("y", function (d) {
              return y(d.value) - 10;
            })
            .attr("height", function (d) {
              return height - y(d.value);
            });

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

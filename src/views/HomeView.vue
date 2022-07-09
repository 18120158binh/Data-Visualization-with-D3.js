<template>
  <!-- <line-chart v-bind="lineChartProps"></line-chart> -->

  <input
    type="radio"
    id="one"
    value="One"
    v-model="radioValue"
    @click="onClickRadio"
  />
  <label for="one">One</label>

  <input
    type="radio"
    id="two"
    value="Two"
    v-model="radioValue"
    @click="onClickRadio"
  />
  <label for="two">Two</label>

  <div ref="barchart"></div>

  <select v-model="selectedValue" @click="onClickSelect">
    <option disabled value="">Please select one</option>
    <option value="One">A</option>
    <option value="Two">B</option>
  </select>
  <div ref="piechart"></div>
  <div ref="areachart"></div>
</template>

<script>
import * as d3 from "d3";
// import { LineChart } from "./components/LineChart.vue";
import areachart_data from "../data/areachart_data.json";
import scatterplot_data from "../data/scatterplot_data.json";

export default {
  name: "HomeView",
  data() {
    return {
      thongkeData: [
        {
          thang: 1,
          doanhthu: 10000,
        },
        {
          thang: 2,
          doanhthu: 20000,
        },
        {
          thang: 3,
          doanhthu: 30000,
        },
      ],
      thongkeData1: [
        {
          thang: 1,
          doanhthu: 10000,
        },
        {
          thang: 2,
          doanhthu: 20000,
        },
        {
          thang: 3,
          doanhthu: 30000,
        },
        {
          thang: 4,
          doanhthu: 10000,
        },
        {
          thang: 5,
          doanhthu: 20000,
        },
        {
          thang: 6,
          doanhthu: 30000,
        },
      ],
      data1: [
        {
          title: "a",
          number: 9,
        },
        {
          title: "b",
          number: 20,
        },
        {
          title: "c",
          number: 30,
        },
        {
          title: "d",
          number: 8,
        },
        {
          title: "e",
          number: 12,
        },
      ],
      data2: [
        {
          title: "a",
          number: 6,
        },
        {
          title: "b",
          number: 16,
        },
        {
          title: "c",
          number: 20,
        },
        {
          title: "d",
          number: 14,
        },
        {
          title: "e",
          number: 19,
        },
        {
          title: "f",
          number: 12,
        },
      ],
      radioValue: "",
      selectedValue: "",
    };
  },
  computed: {
    dayData() {
      return [
        {
          name: "2 times",
          values: [
            {
              date: "14/09/2018",
              value: "18",
            },
            {
              date: "15/09/2018",
              value: "9",
            },
            {
              date: "16/09/2018",
              value: "4",
            },
            {
              date: "18/09/2018",
              value: "5",
            },
          ],
        },
        {
          name: "1 time",
          values: [
            {
              date: "14/09/2018",
              value: "13",
            },
            {
              date: "15/09/2018",
              value: "14",
            },
            {
              date: "16/09/2018",
              value: "6",
            },
            {
              date: "18/09/2018",
              value: "14",
            },
          ],
        },
        {
          name: "4 - 5 times",
          values: [
            {
              date: "14/09/2018",
              value: "5",
            },
            {
              date: "15/09/2018",
              value: "4",
            },
            {
              date: "18/09/2018",
              value: "1",
            },
          ],
        },
        {
          name: "6 - 8 times",
          values: [
            {
              date: "14/09/2018",
              value: "3",
            },
          ],
        },
        {
          name: "more than 8 times",
          values: [
            {
              date: "14/09/2018",
              value: "2",
            },
            {
              date: "18/09/2018",
              value: "2",
            },
          ],
        },
        {
          name: "3 times",
          values: [
            {
              date: "14/09/2018",
              value: "11",
            },
            {
              date: "15/09/2018",
              value: "3",
            },
            {
              date: "16/09/2018",
              value: "3",
            },
            {
              date: "18/09/2018",
              value: "4",
            },
          ],
        },
      ];
    },
    lineChartProps() {
      return {
        xAxisLabel: "Timeline",
        yAxisLabel: "Value",
        xKey: "date",
        yKey: "value",
        interval: "day",
        data: this.dayData,
      };
    },
  },
  components: {
    // LineChart
  },
  methods: {
    update(data, svg, radius, color) {
      // Compute the position of each group on the pie:
      var pie = d3
        .pie()
        .value(function (d) {
          return d.value;
        })
        .sort(function (a, b) {
          console.log(a);
          return d3.ascending(a.key, b.key);
        }); // This make sure that group order remains the same in the pie chart
      var data_ready = pie(d3.entries(data));

      // map to data
      var u = svg.selectAll("path").data(data_ready);

      // Build the pie chart: Basically, each part of the pie is a path that we build using the arc function.
      u.enter()
        .append("path")
        .merge(u)
        .transition()
        .duration(1000)
        .attr("d", d3.arc().innerRadius(0).outerRadius(radius))
        .attr("fill", function (d) {
          return color(d.data.key);
        })
        .attr("stroke", "white")
        .style("stroke-width", "2px")
        .style("opacity", 1);

      // remove the group that is not present anymore
      u.exit().remove();
    },
    onClickRadio() {
      if (this.radioValue === "One") {
        this.createBarChart(this.thongkeData);
      } else {
        this.createBarChart(this.thongkeData1);
      }
    },
    onClickSelect() {
      if (this.selectedValue === "One") {
        this.createPieChart(this.data1);
      } else {
        this.createPieChart(this.data2);
      }
    },
    createBarChart(data) {
      let checkExist = d3.select(this.$refs.barchart).select("svg");
      if (checkExist) {
        checkExist.remove();
      }
      let svg = d3
        .select(this.$refs.barchart)
        .append("svg")
        .attr("width", 400)
        .attr("height", 300);
      var width = svg.attr("width") - 200;
      var height = svg.attr("height") - 200;
      svg
        .append("text")
        .attr("transform", "translate(100,0)")
        .attr("x", 50)
        .attr("y", 50)
        .attr("font-size", "15px")
        .text("Tong doanh thu");
      var xScale = d3.scaleBand().range([0, width]).padding(0.4);
      var yScale = d3.scaleLinear().range([height, 0]);

      var g = svg
        .append("g")
        .attr("transform", "translate(" + 100 + "," + 100 + ")");
      xScale.domain(
        data.map(function (d) {
          return d.thang;
        })
      );
      yScale.domain([
        0,
        d3.max(data, function (d) {
          return d.doanhthu;
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
        .attr("y", height - 80)
        .attr("x", width + 20)
        .attr("text-anchor", "end")
        .attr("stroke", "black")
        .text("Month");
      g.selectAll(".bar")
        .data(data)
        .enter()
        .append("rect")
        .attr("class", "bar")
        .attr("x", function (d) {
          return xScale(d.thang);
        })
        .attr("y", function (d) {
          return yScale(d.doanhthu);
        })
        .attr("width", xScale.bandwidth())
        .attr("height", function (d) {
          return height - yScale(d.doanhthu);
        });
    },
    createPieChart(data) {
      let checkExist = d3.select(this.$refs.piechart).select("svg");
      if (checkExist) {
        checkExist.remove();
      }
      // set the dimensions and margins of the graph
      var width = 500;
      var height = 400;
      // var margin = 40;
      var svg = d3
        .select(this.$refs.piechart)
        .append("svg")
        .attr("width", width)
        .attr("height", height)
        .append("g")
        .attr("transform", "translate(" + width / 2 + "," + height / 2 + ")");
      var radius = Math.min(width, height) / 2;

      var color = d3.scaleOrdinal([
        "#4daf4a",
        "#377eb8",
        "#ff7f00",
        "#984ea3",
        "#e41a1c",
      ]);

      var pie = d3.pie().value(function (d) {
        return d.number;
      });

      var path = d3
        .arc()
        .outerRadius(radius - 10)
        .innerRadius(100);

      var label = d3
        .arc()
        .outerRadius(radius)
        .innerRadius(radius - 80);

      var arc = svg
        .selectAll(".arc")
        .data(pie(data))
        .enter()
        .append("g")
        .attr("class", "arc");

      arc
        .append("path")
        .attr("d", path)
        .attr("fill", function (d, i) {
          // console.log(d);
          return color(i);
        });

      arc
        .append("text")
        .attr("transform", function (d) {
          return "translate(" + label.centroid(d) + ")";
        })
        .text(function (d) {
          return d.title;
        });

      svg
        .append("g")
        .attr("transform", "translate(" + (width / 2 - 380) + "," + -190 + ")")
        .append("text")
        .text("Browser use statistics - Jan 2017")
        .attr("class", "title");
    },
    createBarChartWithAnimated() {
      let svg = d3
        .select("#app")
        .append("svg")
        .attr("width", 600)
        .attr("height", 500);
      var margin = 200;
      var width = svg.attr("width") - margin;
      var height = svg.attr("height") - margin;

      svg
        .append("text")
        .attr("transform", "translate(100,0)")
        .attr("x", 50)
        .attr("y", 50)
        .attr("font-size", "24px")
        .text("Tong doanh thu");

      var x = d3.scaleBand().range([0, width]).padding(0.4),
        y = d3.scaleLinear().range([height, 0]);

      var g = svg
        .append("g")
        .attr("transform", "translate(" + 100 + "," + 100 + ")");
      let data = this.thongkeData;
      x.domain(
        data.map(function (d) {
          return d.thang;
        })
      );
      y.domain([
        0,
        d3.max(data, function (d) {
          return d.doanhthu;
        }),
      ]);

      g.append("g")
        .attr("transform", "translate(0," + height + ")")
        .call(d3.axisBottom(x))
        .append("text")
        .attr("y", height - 280)
        .attr("x", width + 10)
        .attr("text-anchor", "end")
        .attr("stroke", "black")
        .text("Thang");

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
        .on("mouseover", function (d) {
          d3.select(this).attr("class", "highlight");
          d3.select(this)
            .transition() // adds animation
            .duration(400)
            .attr("width", x.bandwidth() + 5)
            .attr("y", function (d) {
              return y(d.doanhthu) - 10;
            })
            .attr("height", function (d) {
              return height - y(d.doanhthu) + 10;
            });

          g.append("text")
            .attr("class", "val")
            .attr("x", function () {
              return x(d.thang);
            })
            .attr("y", function () {
              return y(d.doanhthu) - 15;
            })
            .text(function () {
              return ["$" + d.doanhthu]; // value of the text
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
              return y(d.doanhthu);
            })
            .attr("height", function (d) {
              return height - y(d.doanhthu);
            });

          d3.selectAll(".val").remove();
        }) //Add listener for the mouseout event
        .attr("x", function (d) {
          return x(d.thang);
        })
        .attr("y", function (d) {
          return y(d.doanhthu);
        })
        .attr("width", x.bandwidth())
        .transition()
        .ease(d3.easeLinear)
        .duration(400)
        .delay(function (d, i) {
          return i * 50;
        })
        .attr("height", function (d) {
          return height - y(d.doanhthu);
        });
    },
    createAreaChart() {
      // set the dimensions and margins of the graph
      var margin = { top: 20, right: 30, bottom: 30, left: 55 };
      var width = 460 - margin.left - margin.right;
      var height = 400 - margin.top - margin.bottom;

      // append the svg object to the body of the page
      var svg = d3
        .select(this.$refs.areachart)
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
        .append("g")
        .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

      let data = areachart_data;
      // List of groups = header of the csv files
      var keys = [
        "Amanda",
        "Ashley",
        "Betty",
        "Deborah",
        "Dorothy",
        "Helen",
        "Linda",
        "Patricia",
      ];

      // Add X axis
      var x = d3
        .scaleLinear()
        .domain(
          d3.extent(data, function (d) {
            return d.year;
          })
        )
        .range([0, width]);
      svg
        .append("g")
        .attr("transform", "translate(0," + height + ")")
        .call(d3.axisBottom(x).ticks(5));

      // Add Y axis
      var y = d3.scaleLinear().domain([0, 200000]).range([height, 0]);
      svg.append("g").call(d3.axisLeft(y));

      // color palette
      var color = d3
        .scaleOrdinal()
        .domain(keys)
        .range([
          "#e41a1c",
          "#377eb8",
          "#4daf4a",
          "#984ea3",
          "#ff7f00",
          "#ffff33",
          "#a65628",
          "#f781bf",
        ]);

      //stack the data?
      var stackedData = d3.stack().keys(keys)(data);
      //console.log("This is the stack result: ", stackedData)

      // Show the areas
      svg
        .selectAll("mylayers")
        .data(stackedData)
        .enter()
        .append("path")
        .style("fill", function (d) {
          console.log(d.key);
          return color(d.key);
        })
        .attr(
          "d",
          d3
            .area()
            .x(function (d) {
              return x(d.data.year);
            })
            .y0(function (d) {
              return y(d[0]);
            })
            .y1(function (d) {
              return y(d[1]);
            })
        );
    },
    createScatterPlot() {
      // set the dimensions and margins of the graph
      var margin = { top: 10, right: 30, bottom: 30, left: 60 };
      var width = 460 - margin.left - margin.right;
      var height = 400 - margin.top - margin.bottom;

      // append the svg object to the body of the page
      var svg = d3
        .select("#app")
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
        .append("g")
        .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

      //Read the data
      let data = scatterplot_data;
      // Add X axis
      var x = d3.scaleLinear().domain([0, 4000]).range([0, width]);
      svg
        .append("g")
        .attr("transform", "translate(0," + height + ")")
        .call(d3.axisBottom(x));

      // Add Y axis
      var y = d3.scaleLinear().domain([0, 500000]).range([height, 0]);
      svg.append("g").call(d3.axisLeft(y));

      // Add dots
      svg
        .append("g")
        .selectAll("dot")
        .data(data)
        .enter()
        .append("circle")
        .attr("cx", function (d) {
          return x(d.GrLivArea);
        })
        .attr("cy", function (d) {
          return y(d.SalePrice);
        })
        .attr("r", 1.5)
        .style("fill", "#69b3a2");
    },
  },
  async created() {
    setTimeout(() => {
      this.createAreaChart();
    }, 200);
  },
};
</script>
<style>
.bar {
  fill: steelblue;
}

.arc text {
  font: 10px sans-serif;
  text-anchor: middle;
}

.arc path {
  stroke: #fff;
}

.title {
  fill: teal;
  font-weight: bold;
}
.highlight {
  fill: orange;
}
</style>

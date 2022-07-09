<template>
  <div ref="piechart" @click="clickLoaisp"></div>
</template>

<script>
import * as d3 from "d3";

export default {
  name: "PieChart",
  props: {
    data: { required: true },
    titleChart: { required: true },
    widthChart: { required: true },
    heightChart: { required: true },
    type: { required: true },
  },
  data() {
    return{
      loaisp: '',
    }
  },
  created() {
    setTimeout(() => {
      if(this.type == "1"){
        this.createPieChart(JSON.parse(JSON.stringify(this.data)));
      }
      else if(this.type == "3"){
        this.createPieChartMini(JSON.parse(JSON.stringify(this.data)));
      }
      else {
        this.createPieChartNoEvent(JSON.parse(JSON.stringify(this.data)))
      }
    }, 200);
  },
  computed: {
    getLoaiSP(){
      return this.loaisp;
    },
    setLoaiSP(choose){
      this.loaisp = choose;
      return this.loaisp;
    },
  },
  methods: {
    clickLoaisp(e, loaisp){
      this.$emit('click-loaisp', localStorage.getItem("LoaiSP"))
    },
    createPieChart(data) {
      var piecolor = [];
      for (let i = 0; i < data.length; i++) {
        piecolor.push(d3.schemeCategory10[i]);
      }
      var categorical = data.map(function (d) {
        return d.key;
      });
      let checkExist = d3.select(this.$refs.piechart).select("svg");
      var sumValue = 0;
      data.map(function (d) {
        sumValue += d.value;
      });
      if (checkExist) {
        checkExist.remove();
      }
      // set the dimensions and margins of the graph
      var width = parseInt(this.widthChart);
      var height = parseInt(this.heightChart);
      var margin = 40;
      var piewidth = width / 2 + margin + 50;

      var svg = d3
        .select(this.$refs.piechart)
        .append("svg")
        .attr("width", width)
        .attr("height", height)
        .append("g")
        .attr("transform", "translate(" + piewidth + "," + height / 2 + ")");

      let y = -128;
      let cy = -130;
      for (let i = 0; i < categorical.length; i++) {
        svg
          .append("circle")
          .attr("cx", -360)
          .attr("cy", cy)
          .attr("r", 8)
          .style("fill", piecolor[i]);
        svg
          .append("text")
          .attr("x", -350)
          .attr("y", y)
          .text(categorical[i])
          .style("font-size", "15px")
          .attr("alignment-baseline", "middle");
        y += 30;
        cy += 30;
      }

      svg
        .append("g")
        .attr("transform", "translate(" + (width / 2 - 530) + "," + -180 + ")")
        .append("text")
        .text(this.titleChart)
        .attr("class", "title");

      var radius = Math.min(width, height) / 2 - margin;

      var color = d3.scaleOrdinal(piecolor);
      var color_arc = {}
      var pie = d3.pie().value(function (d) {
        return d.value;
      });

      var path = d3
        .arc()
        .outerRadius(radius - 10)
        .innerRadius(0);

      var label = d3
        .arc()
        .outerRadius(radius)
        .innerRadius(radius - 120);

      var arc = svg
        .selectAll(".arc")
        .data(pie(data))
        .enter()
        .append("g")
        .attr("class", "arc");

      var tooltip = d3
        .select(this.$refs.piechart)
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
        tooltip.transition().duration(100).style("opacity", 1);
        tooltip
          .html(
            "Doanh số: " + d.data.value
          )
      };
      var mousemove = function (event, d) {
        console.log(tooltip);
        tooltip
          .style("left", d3.pointer(event)[0] + 500 + "px") // It is important to put the +90: other wise the tooltip is exactly where the point is an it creates a weird effect
          .style("top", d3.pointer(event)[1] + 700 + "px");
      };
      var mouseleave = function (d) {
        tooltip.transition().duration(100).style("opacity", 0);
      };

      arc
        .append("path")
        .attr("d", path)
        .attr("fill", function (d, i) {
          color_arc[d.data.key] = color(i)
          return color(i);
        });

      arc
        .append("text")
        .attr("transform", function (d) {
          return "translate(" + label.centroid(d) + ")";
        })
        .style("font-size", "15px")
        .text(function (d) {
          return ((d.value * 100) / sumValue).toFixed(2) + "%";
        })
      arc.on('click', function (event, d) {
        localStorage.setItem("LoaiSP", d.data.key)
        var checkClicked = d3.select(".highlight")
        var checkSelected = d3.select(this).attr('class') != 'highlight'
        if (checkClicked._groups[0][0] != null) {
          checkClicked.attr("class", "arc");
          checkClicked.append("path")
            .attr("d", path)
            .attr("fill", color_arc[checkClicked._groups[0][0].__data__.data.key]);
          checkClicked.append("text")
            .attr("transform", function (d) {
              return "translate(" + label.centroid(d) + ")";
            })
            .style("font-size", "15px")
            .text(function (d) {
              return ((d.value * 100) / sumValue).toFixed(1) + "%";
            })
        }
        if (checkSelected) {
          d3.select(this).attr("class", "highlight");
          d3.select(this).append("path")
          .attr("d", path)
          .attr("fill", red);
          d3.select(this).append("text")
            .attr("transform", function (d) {
              return "translate(" + label.centroid(d) + ")";
            })
            .style("font-size", "15px")
            .text(function (d) {
              console.log(d)
              return d.key;
            })
        }
        if(checkSelected == false){
          localStorage.setItem("LoaiSP",'')
        }
        this.clickLoaisp();
      });
      arc
        .on("mouseover", mouseover)
        .on("mousemove", mousemove)
        .on("mouseleave", mouseleave);
    },
    createPieChartNoEvent(data) {
      var piecolor = [];
      for (let i = 0; i < data.length; i++) {
        piecolor.push(d3.schemeSet2[i]);
      }
      var categorical = data.map(function (d) {
        return d.key;
      });
      let checkExist = d3.select(this.$refs.piechart).select("svg");
      var sumValue = 0;
      data.map(function (d) {
        sumValue += d.value;
      });
      if (checkExist) {
        checkExist.remove();
      }
      // set the dimensions and margins of the graph
      var width = parseInt(this.widthChart);
      var height = parseInt(this.heightChart);
      var margin = 50;
      var piewidth = width / 2 + margin + 20;
      var pieheight = height / 2 + 50

      var svg = d3
        .select(this.$refs.piechart)
        .append("svg")
        .attr("width", width)
        .attr("height", height)
      
      let y = 38;
      let cy = 38;
      for (let i = 0; i < categorical.length; i++) {
        if (categorical[i].length > 80){
          let text1 = "Hàng nhận được bị thiếu/sai/bể vỡ/không hoạt động/"
          let text2 = "khác mô tả/đã qua sử dụng/giả nhái"
          svg
          .append("circle")
          .attr("cx", 10)
          .attr("cy", cy)
          .attr("r", 8)
          .style("fill", piecolor[i]);
          svg
            .append("text")
            .attr("x", 20)
            .attr("y", y)
            .text(text1)
            .style("font-size", "15px")
            .attr("alignment-baseline", "middle");
          y += 30;
          svg
            .append("text")
            .attr("x", 20)
            .attr("y", y)
            .text(text2)
            .style("font-size", "15px")
            .attr("alignment-baseline", "middle");
          y += 30;
          cy += 30;
        }
        else{
        svg
          .append("circle")
          .attr("cx", 10)
          .attr("cy", cy)
          .attr("r", 8)
          .style("fill", piecolor[i]);
        svg
          .append("text")
          .attr("x", 20)
          .attr("y", y)
          .text(categorical[i])
          .style("font-size", "15px")
          .attr("alignment-baseline", "middle");
        y += 30;
        cy += 30;
        }
      }

      svg
        .append("text")
        .attr("x", width/2 - 50)
        .attr("y", 20)
        .text(this.titleChart)
        .attr("class", "title");

      svg = svg.append("g")
        .attr("transform", "translate(" + piewidth + "," + pieheight + ")");

      var radius = Math.min(width, height) / 2 - margin;

      var color = d3.scaleOrdinal(piecolor);
      var color_arc = {}
      var pie = d3.pie().value(function (d) {
        return d.value;
      });

      var path = d3
        .arc()
        .outerRadius(radius - 10)
        .innerRadius(0);

      var label = d3
        .arc()
        .outerRadius(radius)
        .innerRadius(radius - 100);

      var arc = svg
        .selectAll(".arc")
        .data(pie(data))
        .enter()
        .append("g")
        .attr("class", "arc");

      var tooltip = d3
        .select(this.$refs.piechart)
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
        tooltip.transition().duration(100).style("opacity", 1);
        tooltip
          .html(
            "Tổng đơn: " + d.data.value
          )
      };
      var mousemove = function (event, d) {
        tooltip
          .style("left", d3.pointer(event)[0] + 900 + "px") // It is important to put the +90: other wise the tooltip is exactly where the point is an it creates a weird effect
          .style("top", d3.pointer(event)[1] + 300 + "px");
      };
      var mouseleave = function (d) {
        tooltip.transition().duration(100).style("opacity", 0);
      };

      arc
        .append("path")
        .attr("d", path)
        .attr("fill", function (d, i) {
          color_arc[d.data.key] = color(i)
          return color(i);
        });

      arc
        .append("text")
        .attr("transform", function (d) {
          return "translate(" + label.centroid(d) + ")";
        })
        .style("font-size", "15px")
        .text(function (d) {
          return ((d.value * 100) / sumValue).toFixed(2) + "%";
        });
      arc
        .on("mouseover", mouseover)
        .on("mousemove", mousemove)
        .on("mouseleave", mouseleave);
    },
    createPieChartMini(data) {
      var piecolor = [];
      for (let i = 0; i < data.length; i++) {
        piecolor.push(d3.schemeCategory10[i]);
      }
      var categorical = data.map(function (d) {
        return d.key;
      });
      let checkExist = d3.select(this.$refs.piechart).select("svg");
      var sumValue = 0;
      data.map(function (d) {
        sumValue += d.value;
      });
      if (checkExist) {
        checkExist.remove();
      }
      // set the dimensions and margins of the graph
      var width = parseInt(this.widthChart);
      var height = parseInt(this.heightChart);
      var margin = 50;
      var piewidth = width / 2 + margin;
      var pieheight = height / 2;

      var svg = d3
        .select(this.$refs.piechart)
        .append("svg")
        .attr("width", width)
        .attr("height", height)
      
      let y = 58;
      let cy = 58;
      for (let i = 0; i < categorical.length; i++) {
        if (categorical[i].length > 80){
          let text1 = "Hàng nhận được bị thiếu/sai/bể vỡ/không hoạt động/"
          let text2 = "khác mô tả/đã qua sử dụng/giả nhái"
          svg
          .append("circle")
          .attr("cx", 10)
          .attr("cy", cy)
          .attr("r", 8)
          .style("fill", piecolor[i]);
          svg
            .append("text")
            .attr("x", 20)
            .attr("y", y)
            .text(text1)
            .style("font-size", "15px")
            .attr("alignment-baseline", "middle");
          y += 30;
          svg
            .append("text")
            .attr("x", 20)
            .attr("y", y)
            .text(text2)
            .style("font-size", "15px")
            .attr("alignment-baseline", "middle");
          y += 30;
          cy += 30;
        }
        else{
        svg
          .append("circle")
          .attr("cx", 10)
          .attr("cy", cy)
          .attr("r", 8)
          .style("fill", piecolor[i]);
        svg
          .append("text")
          .attr("x", 20)
          .attr("y", y)
          .text(categorical[i])
          .style("font-size", "15px")
          .attr("alignment-baseline", "middle");
        y += 30;
        cy += 30;
        }
      }

      svg
        .append("text")
        .attr("x", width/2 - 130)
        .attr("y", 20)
        .text(this.titleChart)
        .attr("class", "title");

      svg = svg.append("g")
        .attr("transform", "translate(" + piewidth + "," + pieheight + ")");

      var radius = Math.min(width, height) / 2 - margin;

      var color = d3.scaleOrdinal(piecolor);
      var color_arc = {}
      var pie = d3.pie().value(function (d) {
        return d.value;
      });

      var path = d3
        .arc()
        .outerRadius(radius - 10)
        .innerRadius(0);

      var label = d3
        .arc()
        .outerRadius(radius)
        .innerRadius(radius - 100);

      var arc = svg
        .selectAll(".arc")
        .data(pie(data))
        .enter()
        .append("g")
        .attr("class", "arc");

      var tooltip = d3
        .select(this.$refs.piechart)
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
        tooltip.transition().duration(100).style("opacity", 1);
        tooltip
          .html(
            "Số lượng khách: " + d.data.value
          )
      };
      var mousemove = function (event, d) {
        tooltip
          .style("left", d3.pointer(event)[0] + 300 + "px") // It is important to put the +90: other wise the tooltip is exactly where the point is an it creates a weird effect
          .style("top", d3.pointer(event)[1] + 300 + "px");
      };
      var mouseleave = function (d) {
        tooltip.transition().duration(100).style("opacity", 0);
      };

      arc
        .append("path")
        .attr("d", path)
        .attr("fill", function (d, i) {
          color_arc[d.data.key] = color(i)
          return color(i);
        });

      arc
        .append("text")
        .attr("transform", function (d) {
          return "translate(" + label.centroid(d) + ")";
        })
        .style("font-size", "15px")
        .text(function (d) {
          return ((d.value * 100) / sumValue).toFixed(2) + "%";
        });
      arc
        .on("mouseover", mouseover)
        .on("mousemove", mousemove)
        .on("mouseleave", mouseleave);
    },
  },
};
</script>

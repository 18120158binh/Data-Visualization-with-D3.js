<template>
  <div style="background-color: #d9eff5">
    <section class="welcome p-t-10">
      <div class="container">
        <div class="row">
          <div class="col-md-12">
            <h1 class="title-4">Dashboard</h1>
            <hr class="line-seprate" />
          </div>
        </div>
      </div>
    </section>
    <!-- STATISTIC-->
    <section class="statistic statistic2">
      <div class="container">
        <div class="row">
          <div class="col-md-6 col-lg-3">
            <div class="statistic__item statistic__item--green">
              <h2 class="number">21.900.700</h2>
              <span class="desc">Tổng doanh thu</span>
              <div class="icon">
                <i class="zmdi zmdi-money"></i>
              </div>
            </div>
          </div>
          <div class="col-md-6 col-lg-3">
            <div class="statistic__item statistic__item--orange">
              <h2 class="number">300</h2>
              <span class="desc">Số lượng sản phẩm bán ra</span>
              <div class="icon">
                <i class="zmdi zmdi-external-link-square"></i>
              </div>
            </div>
          </div>
          <div class="col-md-6 col-lg-3">
            <div class="statistic__item statistic__item--blue">
              <h2 class="number">4.9</h2>
              <span class="desc">Đánh giá trung bình</span>
              <div class="icon">
                <i class="zmdi zmdi-calendar-note"></i>
              </div>
            </div>
          </div>
          <div class="col-md-6 col-lg-3">
            <div class="statistic__item statistic__item--red">
              <h2 class="number">100</h2>
              <span class="desc">Người theo dõi</span>
              <div class="icon">
                <i class="zmdi zmdi-account"></i>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
    <!-- END STATISTIC-->

    <!-- STATISTIC CHART-->
    <section class="statistic-chart">
      <div class="container">
        <div class="row">
          <div class="col-md-12">
            <h3 class="title-5 m-b-35">statistics</h3>
          </div>
        </div>
        <div class="row">
          <div class="col-md-12 col-lg-12">
            <div class="statistic-chart-1" style="width: 100%; height: 900px">
              <h3 class="title-3 m-b-30">
                Tổng doanh số và doanh thu của cửa hàng
              </h3>
              <treeselect
                v-model="treeselectValue"
                :options="options"
                v-on:input="onClickForSaleRevenue"
                v-on:select="onClickForSaleRevenue"
                v-on:deselect="onClickForSaleRevenue"
                v-on:close="onClickForSaleRevenue"
                :multiple="false"
              />
              <div class="table-responsive">
                <div>
                  <bar-chart-two-y-axis :data1="DoanhThu" :data2="DoanhSo" :key="DoanhSo"></bar-chart-two-y-axis>
                </div>
                <div>
                  <pie-chart
                    style="display: inline-block"
                    :data="TongBanLoaiSP"
                    titleChart="Tổng lượng bán theo từng loại sản phẩm"
                    widthChart="560"
                    heightChart="400"
                    type="1"
                    :key="TongBanLoaiSP"
                    @click-loaisp="changeLoaisp"
                  ></pie-chart>
                  <horizontal-bar-chart
                    style="display: inline-block"
                    :data="TopSPBanChay"
                    :key="TopSPBanChay"
                  ></horizontal-bar-chart>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="row">
          <div class="col-md-12 col-lg-12">
            <div
              class="statistic-chart-1"
              style="width: 1300px; height: 1000px"
            >
              <h3 class="title-3 m-b-30">Khách hàng</h3>
              <div class="table-responsive">
                <div>
                  <pie-chart
                  style="display: inline-block" 
                  :data="NhomTuoiKH"
                  :key="NhomTuoiKH"
                  widthChart="360"
                  heightChart="330"
                  titleChart="Tỉ lệ khách hàng theo nhóm tuổi"
                  type="3"
                  ></pie-chart>
                  <horizontal-stacked-bar-chart
                style="display: inline-block" 
                :data="DoTuoi_DanhMucMua" 
                :key="DoTuoi_DanhMucMua">
                </horizontal-stacked-bar-chart>
                <input type="radio" id="one" value="One" v-model="ReorderPicked" @change="onClickReorder"/>
                <label for="one">Reorder Asc</label>
                &nbsp;
                <input type="radio" id="two" value="Two" v-model="ReorderPicked" @change="onClickReorder"/>
                <label for="two">Reorder Desc</label>

                </div>
                <histogram :data="SoLuongBanSanPhamTheoGia" :key="SoLuongBanSanPhamTheoGia"></histogram>
                
              </div>
            </div>
          </div>
        </div>
        <div class="row">
          <div class="col-md-6 col-lg-4">
            <div
              class="statistic-chart-1"
              style="width: 1300px; height: 800px"
            >
              <h3 class="title-3 m-b-30">Tình trạng bán hàng</h3>
              <div class="table-responsive">
                <div>
                  <stacked-bar-chart style="display: inline-block" :data="TinhHinhDonHang" :key="TinhHinhDonHang"></stacked-bar-chart>
                  <div style="display: inline-block">
                    <pie-chart
                    :data="LiDoHuyDon"
                    titleChart="Lí do hủy đơn"
                    widthChart="560"
                    heightChart="330"
                    :key="LiDoHuyDon"
                    type="2"
                    ></pie-chart>
                    <pie-chart
                    :data="LiDoKhieuNai"
                    titleChart="Lí do khiếu nại"
                    widthChart="560"
                    heightChart="330"
                    :key="LiDoKhieuNai"
                    type="2"
                  ></pie-chart>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="row">
          <div class="col-md-12 col-lg-12">
            <div class="statistic-chart-1">
              <h3 class="title-3 m-b-30">Tình hình tồn kho của cửa hàng</h3>
              <div class="table-responsive">
                <many-dot-line-chart :data="TinhHinhTonKho" :key="TinhHinhTonKho"></many-dot-line-chart>
              </div>
            </div>
          </div>
          <!-- <div class="col-md-6 col-lg-6">
            <div class="statistic-chart-1">
              <h3 class="title-3 m-b-30">Tình hình đơn hàng của cửa hàng</h3>
              <div class="table-responsive">
                <stacked-bar-chart :data="TinhHinhDonHang" :key="TinhHinhDonHang"></stacked-bar-chart>
              </div>
            </div>
          </div> -->
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import * as d3 from "d3";
import Treeselect from "vue3-treeselect";
import BarChart from "../components/BarChart";
import Histogram from "../components/Histogram";
import AreaChart from "../components/AreaChart";
import HorizontalBarChart from "../components/HorizontalBarChart";
import "vue3-treeselect/dist/vue3-treeselect.css";
import StackedBarChart from "@/components/StackedBarChart";
import PieChart from "@/components/PieChart.vue";
import ScatterPlot from "@/components/ScatterPlot.vue";
import GroupedBarplot from "@/components/GroupedBarplot.vue";
import ManyDotLineChart from "@/components/ManyDotLineChart.vue";
import BarChartTwoYAxis from "@/components/BarChartTwoYAxis.vue";
import HorizontalStackedBarChart from "@/components/HorizontalStackedBarChart.vue";
import axios from 'axios';

export default {
  name: "Dashboard",
  data() {
    return {
      DoanhSo: [],
      DoanhThu: [],
      TongBanLoaiSP: [],
      TopSPBanChay: [],
      SoLuongBanSanPhamTheoGia: [],
      LuongNhap: [],
      LuongBan: [],
      TinhHinhTonKho: [],
      DoTuoi_DanhMucMua: [],
      DoTuoiDanhMucMuaInit: [],
      options: [
        {
          id: "7days",
          label: "7 ngày gần nhất",
        },
        {
          id: "28days",
          label: "28 ngày gần nhất",
        },
        {
          id: "3months",
          label: "3 tháng vừa qua",
        },
      ],
      TinhHinhDonHang: [],
      LiDoHuyDon: [],
      LiDoKhieuNai: [],
      NhomTuoiKH: [],
      treeselectValue: "7days",
      ReorderPicked: null,
    };
  },
  components: {
    Treeselect,
    BarChart,
    Histogram,
    AreaChart,
    HorizontalBarChart,
    StackedBarChart,
    PieChart,
    ScatterPlot,
    GroupedBarplot,
    ManyDotLineChart,
    BarChartTwoYAxis,
    HorizontalStackedBarChart
  },
  created() {
    axios.get(`https://localhost:44369/api/thongketinhhinhbanhang/doanhthutheothoigian/${this.treeselectValue}`)
    .then(response => {
      this.DoanhThu = JSON.parse(JSON.stringify(response.data))
    })
    .catch(e => {
      this.errors.push(e)
    })
    axios.get(`https://localhost:44369/api/thongketinhhinhbanhang/doanhsotheothoigian/${this.treeselectValue}`)
    .then(response => {
      this.DoanhSo = JSON.parse(JSON.stringify(response.data))
    })
    axios.get(`https://localhost:44369/api/thongketinhhinhbanhang/doanhsotheodanhmuctheothoigian/${this.treeselectValue}`)
    .then(response => {
      this.TongBanLoaiSP = JSON.parse(JSON.stringify(response.data));
    })
    .catch(e => {
      this.errors.push(e)
    })
    axios.get(`https://localhost:44369/api/thongketinhhinhbanhang/top10sptheothoigian/${this.treeselectValue}`)
    .then(response => {
      this.TopSPBanChay = JSON.parse(JSON.stringify(response.data));
      Vue.set(this.TopSPBanChay, JSON.parse(JSON.stringify(response.data)))
    })
    .catch(e => {
      this.errors.push(e)
    })
    axios.get(`https://localhost:44369/api/phantichgiabansoluongmua/7days`)
    .then(response => {
      this.SoLuongBanSanPhamTheoGia = JSON.parse(JSON.stringify(response.data));
    })
    .catch(e => {
      this.errors.push(e)
    })
    axios.get(`https://localhost:44369/api/tonkho/7days`)
    .then(response => {
      this.TinhHinhTonKho = JSON.parse(JSON.stringify(response.data));
    })
    .catch(e => {
      this.errors.push(e)
    })
    axios.get(`https://localhost:44369/api/tinhhinhdonhang/7days`)
    .then(response => {
      this.TinhHinhDonHang = JSON.parse(JSON.stringify(response.data));
    })
    .catch(e => {
      this.errors.push(e)
    })
    axios.get(`https://localhost:44369/api/phantichkhachhang/dotuoidanhmuc`)
    .then(response => {
      this.DoTuoiDanhMucMuaInit = JSON.parse(JSON.stringify(response.data))
      var tmp = JSON.parse(JSON.stringify(response.data))
      var data = [
        {
          key: "BIA, NƯỚC GIẢI KHÁT",
        },
        {
          key: "DẦU ĂN, NƯỚC CHẤM, GIA VỊ",
        },
        {
          key: "BÁNH KẸO CÁC LOẠI",
        },
        {
          key: "MÌ, MIẾN, CHÁO, PHỞ",

        },
        {
          key: "THỰC PHẨM ĐÔNG - MÁT",

        },
        {
          key: "RAU, CỦ, TRÁI CÂY",

        },
        {
          key: "SỮA CÁC LOẠI",
        },
        {
          key: "GẠO, BỘT, ĐỒ KHÔ",
        },
        {
          key: "THỊT, CÁ, TRỨNG, HẢI SẢN",
        },
      ]
      tmp.map(function(d) {
        if (d.key2 === 'BIA, NƯỚC GIẢI KHÁT') {
          // data[0].value.push({key: d.key1, value: d.value})
          data[0][d.key1] = d.value
        }
        if (d.key2 == 'DẦU ĂN, NƯỚC CHẤM, GIA VỊ') {
          // data[1].value.push({key: d.key1, value: d.value})
          data[1][d.key1] = d.value
        }
        if (d.key2 == 'BÁNH KẸO CÁC LOẠI') {
          // data[2].value.push({key: d.key1, value: d.value})
          data[2][d.key1] = d.value
        }
        if (d.key2 == 'MÌ, MIẾN, CHÁO, PHỞ') {
          // data[3].value.push({key: d.key1, value: d.value})
          data[3][d.key1] = d.value
        }
        if (d.key2 == 'THỰC PHẨM ĐÔNG - MÁT') {
          // data[4].value.push({key: d.key1, value: d.value})
          data[4][d.key1] = d.value
        }
        if (d.key2 == 'RAU, CỦ, TRÁI CÂY') {
          // data[5].value.push({key: d.key1, value: d.value})
          data[5][d.key1] = d.value
        }
        if (d.key2 == 'SỮA CÁC LOẠI') {
          // data[6].value.push({key: d.key1, value: d.value})
          data[6][d.key1] = d.value
        }
        if (d.key2 == 'GẠO, BỘT, ĐỒ KHÔ') {
          // data[7].value.push({key: d.key1, value: d.value})
          data[7][d.key1] = d.value
        }
        if (d.key2 == 'THỊT, CÁ, TRỨNG, HẢI SẢN') {
          // data[8].value.push({key: d.key1, value: d.value})
          data[8][d.key1] = d.value
        }
      })

      this.DoTuoi_DanhMucMua = data
    })
    .catch(e => {
      this.errors.push(e)
    })
    axios.get(`https://localhost:44369/api/thongketinhhinhbanhang/chitiethuydon`)
    .then(response => {
      this.LiDoHuyDon = JSON.parse(JSON.stringify(response.data))
    })
    .catch(e => {
      this.errors.push(e)
    })
    axios.get(`https://localhost:44369/api/thongketinhhinhbanhang/chitietkhieunai`)
    .then(response => {
      this.LiDoKhieuNai = JSON.parse(JSON.stringify(response.data))
    })
    .catch(e => {
      this.errors.push(e)
    })
    axios.get(`https://localhost:44369/api/thongketinhhinhbanhang/nhomtuoikh`)
    .then(response => {
      this.NhomTuoiKH = JSON.parse(JSON.stringify(response.data))
    })
    .catch(e => {
      this.errors.push(e)
    })
    setTimeout(() => {
          this.$forceUpdate();
    }, 1000);
  },
  watch: {
    DoanhSo: {
        handler: function () {
				
        },
        deep: true
    },
  },
  methods: {
    async onClickForSaleRevenue() {
      console.log(this.treeselectValue);
    axios.get(`https://localhost:44369/api/thongketinhhinhbanhang/doanhthutheothoigian/${this.treeselectValue}`)
    .then(response => {
      this.DoanhThu = JSON.parse(JSON.stringify(response.data))
    })
    .catch(e => {
      this.errors.push(e)
    })
    axios.get(`https://localhost:44369/api/thongketinhhinhbanhang/doanhsotheothoigian/${this.treeselectValue}`)
    .then(response => {
      this.DoanhSo = JSON.parse(JSON.stringify(response.data))
    })
    axios.get(`https://localhost:44369/api/thongketinhhinhbanhang/doanhsotheodanhmuctheothoigian/${this.treeselectValue}`)
    .then(response => {
      this.TongBanLoaiSP = JSON.parse(JSON.stringify(response.data));
    })
    .catch(e => {
      this.errors.push(e)
    })
    axios.get(`https://localhost:44369/api/thongketinhhinhbanhang/top10sptheothoigian/${this.treeselectValue}`)
    .then(response => {
      this.TopSPBanChay = JSON.parse(JSON.stringify(response.data));
      Vue.set(this.TopSPBanChay, JSON.parse(JSON.stringify(response.data)))
    })
    .catch(e => {
      this.errors.push(e)
    })
    },
    createDotLineChart() {
      // set the dimensions and margins of the graph
      var margin = { top: 10, right: 100, bottom: 30, left: 50 },
        width = 760 - margin.left - margin.right,
        height = 400 - margin.top - margin.bottom;

      let checkExist = d3.select(this.$refs.linechartForExist).select("svg");
      if (checkExist) {
        checkExist.remove();
      }
      let svg = d3
        .select(this.$refs.linechartForExist)
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
        .append("g")
        .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

      //Read the data
      var data = this.tinhHinhTonKho;
      var x = d3
        .scalePoint()
        .domain(
          data.map(function (d) {
            return d.tuan;
          })
        )
        .range([0, width]);
      svg
        .append("g")
        .attr("transform", "translate(0," + height + ")")
        .call(d3.axisBottom(x));
      // Add Y axis
      var y = d3.scaleLinear().domain([0, 350]).range([height, 0]);
      svg.append("g").call(d3.axisLeft(y));
      // Add the line
      svg
        .append("path")
        .datum(data)
        .attr("fill", "none")
        .attr("stroke", "#69b3a2")
        .attr("stroke-width", 1.5)
        .attr(
          "d",
          d3
            .line()
            .x(function (d) {
              return x(d.tuan);
            })
            .y(function (d) {
              return y(d.soluongton);
            })
        );
      // create a tooltip
      var Tooltip = d3
        .select(this.$refs.linechartForExist)
        .append("div")
        .style("opacity", 0)
        .attr("class", "tooltip")
        .style("background-color", "white")
        .style("border", "solid")
        .style("border-width", "2px")
        .style("border-radius", "5px")
        .style("padding", "5px");

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
        .append("g")
        .selectAll("dot")
        .data(data)
        .enter()
        .append("circle")
        .attr("cx", function (d) {
          return x(d.tuan);
        })
        .attr("cy", function (d) {
          return y(d.soluongton);
        })
        .attr("r", 5)
        .attr("fill", "#69b3a2")
        .on("mouseover", mouseover)
        .on("mousemove", mousemove)
        .on("mouseleave", mouseleave);
    },
    onChangeTab(key) {
      this.type = key;
    },
    changeLoaisp(e) {
      if(e != ''){
        axios.get(`https://localhost:44369/api/thongketinhhinhbanhang/doanhthutheothoigian/${this.treeselectValue}/${e}`)
      .then(response => {
        this.DoanhThu = JSON.parse(JSON.stringify(response.data))
      })
      .catch(e => {
        this.errors.push(e)
      })
      axios.get(`https://localhost:44369/api/thongketinhhinhbanhang/doanhsotheothoigian/${this.treeselectValue}/${e}`)
      .then(response => {
        this.DoanhSo = JSON.parse(JSON.stringify(response.data))
      })
      axios.get(`https://localhost:44369/api/thongketinhhinhbanhang/top10sptheothoigian/${this.treeselectValue}/${e}`)
      .then(response => {
        this.TopSPBanChay = JSON.parse(JSON.stringify(response.data));
        Vue.set(this.TopSPBanChay, JSON.parse(JSON.stringify(response.data)))
      })
      .catch(e => {
        this.errors.push(e)
      })
      }
      else{
        this.onClickForSaleRevenue();
      }
    },
    onClickReorder(){
      if(this.ReorderPicked == "Two"){
        var data = [
        {
          key: "DẦU ĂN, NƯỚC CHẤM, GIA VỊ",
        },
        {
          key: "THỰC PHẨM ĐÔNG - MÁT",
        },
        {
          key: "GẠO, BỘT, ĐỒ KHÔ",
        },
        {
          key: "MÌ, MIẾN, CHÁO, PHỞ",
        },
        {
          key: "RAU, CỦ, TRÁI CÂY",
        },
        {
          key: "BIA, NƯỚC GIẢI KHÁT",
        },
        {
          key: "THỊT, CÁ, TRỨNG, HẢI SẢN",
        },
        {
          key: "SỮA CÁC LOẠI",
        },
        {
          key: "BÁNH KẸO CÁC LOẠI",
        },
      ]
      var tmp = this.DoTuoiDanhMucMuaInit
       tmp.map(function(d) {
        if (d.key2 === 'BIA, NƯỚC GIẢI KHÁT') {
          // data[0].value.push({key: d.key1, value: d.value})
          data[5][d.key1] = d.value
        }
        if (d.key2 == 'DẦU ĂN, NƯỚC CHẤM, GIA VỊ') {
          // data[1].value.push({key: d.key1, value: d.value})
          data[0][d.key1] = d.value
        }
        if (d.key2 == 'BÁNH KẸO CÁC LOẠI') {
          // data[2].value.push({key: d.key1, value: d.value})
          data[8][d.key1] = d.value
        }
        if (d.key2 == 'MÌ, MIẾN, CHÁO, PHỞ') {
          // data[3].value.push({key: d.key1, value: d.value})
          data[3][d.key1] = d.value
        }
        if (d.key2 == 'THỰC PHẨM ĐÔNG - MÁT') {
          // data[4].value.push({key: d.key1, value: d.value})
          data[1][d.key1] = d.value
        }
        if (d.key2 == 'RAU, CỦ, TRÁI CÂY') {
          // data[5].value.push({key: d.key1, value: d.value})
          data[4][d.key1] = d.value
        }
        if (d.key2 == 'SỮA CÁC LOẠI') {
          // data[6].value.push({key: d.key1, value: d.value})
          data[7][d.key1] = d.value
        }
        if (d.key2 == 'GẠO, BỘT, ĐỒ KHÔ') {
          // data[7].value.push({key: d.key1, value: d.value})
          data[2][d.key1] = d.value
        }
        if (d.key2 == 'THỊT, CÁ, TRỨNG, HẢI SẢN') {
          // data[8].value.push({key: d.key1, value: d.value})
          data[6][d.key1] = d.value
        }
      })
      this.DoTuoi_DanhMucMua = data
      }
      else{
        var data = [
        {
          key: "BÁNH KẸO CÁC LOẠI",
        },
        {
          key: "SỮA CÁC LOẠI",
        },
        {
          key: "THỊT, CÁ, TRỨNG, HẢI SẢN",
        },
        {
          key: "BIA, NƯỚC GIẢI KHÁT",
        },
        {
          key: "RAU, CỦ, TRÁI CÂY",
        },
        {
          key: "MÌ, MIẾN, CHÁO, PHỞ",
        },
        {
          key: "GẠO, BỘT, ĐỒ KHÔ",
        },
        {
          key: "THỰC PHẨM ĐÔNG - MÁT",
        },
        {
          key: "DẦU ĂN, NƯỚC CHẤM, GIA VỊ",
        },
      ]
      var tmp = this.DoTuoiDanhMucMuaInit
       tmp.map(function(d) {
        if (d.key2 === 'BIA, NƯỚC GIẢI KHÁT') {
          // data[0].value.push({key: d.key1, value: d.value})
          data[3][d.key1] = d.value
        }
        if (d.key2 == 'DẦU ĂN, NƯỚC CHẤM, GIA VỊ') {
          // data[1].value.push({key: d.key1, value: d.value})
          data[8][d.key1] = d.value
        }
        if (d.key2 == 'BÁNH KẸO CÁC LOẠI') {
          // data[2].value.push({key: d.key1, value: d.value})
          data[0][d.key1] = d.value
        }
        if (d.key2 == 'MÌ, MIẾN, CHÁO, PHỞ') {
          // data[3].value.push({key: d.key1, value: d.value})
          data[5][d.key1] = d.value
        }
        if (d.key2 == 'THỰC PHẨM ĐÔNG - MÁT') {
          // data[4].value.push({key: d.key1, value: d.value})
          data[7][d.key1] = d.value
        }
        if (d.key2 == 'RAU, CỦ, TRÁI CÂY') {
          // data[5].value.push({key: d.key1, value: d.value})
          data[4][d.key1] = d.value
        }
        if (d.key2 == 'SỮA CÁC LOẠI') {
          // data[6].value.push({key: d.key1, value: d.value})
          data[1][d.key1] = d.value
        }
        if (d.key2 == 'GẠO, BỘT, ĐỒ KHÔ') {
          // data[7].value.push({key: d.key1, value: d.value})
          data[6][d.key1] = d.value
        }
        if (d.key2 == 'THỊT, CÁ, TRỨNG, HẢI SẢN') {
          // data[8].value.push({key: d.key1, value: d.value})
          data[2][d.key1] = d.value
        }
      })
      this.DoTuoi_DanhMucMua = data
      }
    }
  },
};
</script>

<style lang="scss">
@import "../assets/cooladmin/vendor/font-awesome-4.7/css/font-awesome.min.css";
@import "../assets/cooladmin/vendor/font-awesome-5/css/fontawesome-all.min.css";
@import "../assets/cooladmin/vendor/mdi-font/css/material-design-iconic-font.min.css";
@import "../assets/cooladmin/vendor/bootstrap-4.1/bootstrap.min.css";
@import "../assets/cooladmin/vendor/animsition/animsition.min.css";
@import "../assets/cooladmin/vendor/bootstrap-progressbar/bootstrap-progressbar-3.3.4.min.css";
@import "../assets/cooladmin/vendor/wow/animate.css";
@import "../assets/cooladmin/vendor/css-hamburgers/hamburgers.min.css";
@import "../assets/cooladmin/vendor/slick/slick.css";
@import "../assets/cooladmin/vendor/select2/select2.min.css";
@import "../assets/cooladmin/vendor/perfect-scrollbar/perfect-scrollbar.css";
@import "../assets/cooladmin/css/theme.css";
</style>

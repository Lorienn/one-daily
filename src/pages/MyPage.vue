<template>
  <div id="app">
    <!-- 头像+用户名 -->
    <view class="user">
      <view class="userinfo-avatar">
        <open-data type="userAvatarUrl"></open-data>
      </view>
      <open-data type="userNickName"></open-data>
    </view>

    <!-- 日期选择 -->
    <div class="date">
      <picker mode="date" :value="myMonth" fields="month" @change="handleChange">
        <a>{{myMonth}}</a>
        <img src="/static/drag.png" alt />
      </picker>
    </div>

    <!-- 日记数量 -->
    <div class="addup">已记录 | {{addup}}篇</div>

    <!-- 写日记时间 -->
    <div class="chart">
      <p>本月写日记时间</p>
      <div class="chart_bd">
        <mpvue-echarts ref="pieChart" :echarts="echarts1" @onInit="initChart" />
      </div>
    </div>

    <!-- 心情晴雨表 -->
    <div class="emotion">
      <p>心情晴雨表</p>
      <div class="mood_chart">
        <!-- <div id="chart1" style="width:600px; height: 400px;"></div> -->
        <mpvue-echarts ref="pieChart2" :echarts="echarts" @onInit="initChart2" />
      </div>
    </div>

    <!-- 图库 -->
    <div class="photo">
      <button @click="navTo" style="margin-bottom: 10px;">图库</button>
      <div class="photo_bd">
        <div class="bigImg">
          <img src="../static/donghae.jpg" />
        </div>
        <div class="smallImg">
          <img src="../static/donghae1.jpg" />
          <img src="../static/donghae2.jpg" />
        </div>
      </div>
    </div>

    <!-- 位置 -->
    <div class="map">
      <p>位置</p>
      <map :latitude="latitude" :longitude="longitude" scale="8" :markers="covers"></map>
    </div>
  </div>
</template>

<script>
import * as echarts from "echarts";
import mpvueEcharts from "mpvue-echarts";

export default {
  components: {
    mpvueEcharts,
  },
  data() {
    return {
      echarts,
      latitude: 23.150587,
      longitude: 113.324585,
      covers: [
        {
          id: 1,
          latitude: 23.150587,
          longitude: 113.324585,
        },
        {
          id: 2,
          latitude: 23.008055,
          longitude: 113.347785,
        },
      ],
      addup: 47,
      username: "",
      profilePicURL: "",
      year: "",
      month: "",
      myMonth: "2020-07",
      images: [],
      openid: "",
    };
  },

  onLoad: function () {
    // 查看是否授权
    var that = this;
    wx.getSetting({
      success(res) {
        if (res.authSetting["scope.userInfo"]) {
          // 已经授权，可以直接调用 getUserInfo 获取头像昵称
          wx.getUserInfo().then(function (res) {
            console.log(res.userInfo);
            that.username = res.userInfo.userNickName;
            that.profilePicURL = res.userInfo.avatarUrl;
          });
        }
      },
    });
  },
  methods: {
    initChart() {
      let canvas = this.$refs.pieChart.canvas;
      echarts.setCanvasCreator(() => canvas);
      const chart = echarts.init(canvas, null, {
        width: 330,
        height: 200,
      });
      canvas.setChart(chart);
      chart.setOption({
        color: ["#62cff3"],
        tooltip: {},
        xAxis: {
          data: [
            "0时",
            "1时",
            "2时",
            "3时",
            "4时",
            "5时",
            "6时",
            "7时",
            "8时",
            "9时",
            "10时",
            "11时",
            "12时",
            "13时",
            "14时",
            "15时",
            "16时",
            "17时",
            "18时",
            "19时",
            "20时",
            "21时",
            "22时",
            "23时",
            "24时",
          ],
          axisTick: {
            show: false,
          },
        },
        yAxis: {
          show: false,
        },
        series: [
          {
            name: "次数",
            type: "bar",
            data: [
              5,
              20,
              36,
              10,
              10,
              20,
              5,
              20,
              36,
              10,
              10,
              20,
              5,
              20,
              36,
              10,
              10,
              20,
              5,
              20,
              36,
              10,
              10,
              20,
            ],
          },
        ],
      });
      this.$refs.pieChart.setChart(chart);
      return chart;
    },
    initChart2() {
      let canvas = this.$refs.pieChart2.canvas;
      echarts.setCanvasCreator(() => canvas);
      const chart = echarts.init(canvas, null, {
        width: 330,
        height: 200,
      });
      canvas.setChart(chart);
      chart.setOption({
        color: ["#62cff3"],
        tooltip: {},
        xAxis: {
          data: [
            "1日",
            "2日",
            "3日",
            "4日",
            "5日",
            "6日",
            "7日",
            "8日",
            "9日",
            "10日",
            "11日",
            "12日",
            "13日",
            "14日",
            "15日",
            "16日",
            "17日",
            "18日",
            "19日",
            "20日",
            "21日",
            "22日",
            "23日",
            "24日",
            "25日",
            "26日",
            "27日",
            "28日",
            "29日",
            "30日",
            "31日",
          ],
        },
        yAxis: {
          show: false,
        },
        series: [
          {
            type: "line",
            // symbol: "none",
            smooth: true, //设置圆滑曲线
            data: [
              3,
              1,
              2,
              1,
              5,
              4,
              1,
              4,
              2,
              1,
              4,
              1,
              4,
              2,
              1,
              3,
              1,
              4,
              2,
              5,
              2,
              1,
              5,
              4,
              1,
              1,
              3,
              1,
              4,
              2,
              2,
            ],
          },
        ],
      });
      this.$refs.pieChart2.setChart(chart);
      return chart;
    },
    navTo() {
      wx.navigateTo({ url: "/pages/photoAlbum/index" });
    },
    handleChange(e) {
      this.myMonth = e.detail.value;
      this.handleDate();
      this.getData();
    },
    handleDate() {
      this.year = +this.myMonth.split("-")[0];
      this.month = this.myMonth.split("-")[1];
    },
    getData() {
      // wx.cloud.init({
      //   env: "xgmoments-84rox",
      // });
      // var that = this;
      // var db = wx.cloud.database();
      // db.collection("example")
      //   .where({ year: that.year, month: that.month })
      //   .get()
      //   .then(function (res) {
      //     const {data} = res
      //     that.openid = data[0]["_openid"];
      //     data.map((item) => {
      //       that.timeItems[item.hour]++;
      //     });
      // console.log(that.timeItems);
      // });
      db.collection("example")
        .where({
          _openid: this.openid,
        })
        .count({
          //统计当前用户所发布的所有日记数量
          success: (res) => {
            that.addup = res.total;
          },
        });
    },
  },
};
</script>


<style>
#app {
  width: 90%;
  padding-top: 50px;
  margin: 0 auto 50px auto;
}
.user,
.date,
.addup,
.chart,
.emotion,
.photo,
.map {
  margin-top: 15px;
  box-shadow: 0 0 20px rgba(30, 30, 30, 0.1);
  border-radius: 5px;
}

.user {
  margin: 0;
  height: 320rpx;
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
}
.date {
  height: 50px;
  line-height: 50px;
  text-align: center;
  font-size: 13px;
}
.date a {
  display: inline;
}
.date img {
  width: 15px;
  height: 15px;
  margin: 2px 0 0 3px;
}
.addup {
  height: 50px;
  line-height: 50px;
  font-size: 14px;
  text-align: center;
}
.chart {
  height: 200px;
}
.emotion {
  height: 220px;
}
.photo {
  height: 250px;
  padding: 5px;
}
.map {
  height: 220px;
}

.chart p,
.map p,
.emotion p {
  font-size: 13px;
  padding: 15px 0 0 15px;
}
.map map {
  margin: 15px auto 0 auto;
}
.chart .chart_bd,
.emotion .mood_chart {
  height: 180px;
}

.userinfo-avatar {
  overflow: hidden;
  display: block;
  width: 160rpx;
  height: 160rpx;
  margin: 20rpx;
  margin-top: 50rpx;
  border-radius: 50%;
  border: 2px solid #fff;
  box-shadow: 3px 3px 10px rgba(0, 0, 0, 0.2);
}

.photo_bd {
  display: flex;
  width: 280px;
  margin: 0 auto;
}
.bigImg img {
  display: block;
  width: 180px;
  height: 180px;
}
.smallImg {
  margin-left: 10px;
}
.smallImg img {
  display: block;
  width: 90px;
  height: 90px;
}
</style>>

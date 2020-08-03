    // 这是edit页面
<template>
  <view class="content">
    <view class="uni-list" id="header">
      <view class="uni-list-cell">
        <view class="uni-list-cell-db">
          <picker
            mode="date"
            :value="fulldate"
            :start="startDate"
            :end="endDate"
            @change="bindDateChange"
          >
            <view class="uni-input" v-if="fulldate" id="chooseDate">{{fulldate}}</view>
            <text v-else id="today">
              {{month}}月{{date}}日
              <text id="fullTime">/ {{fullTime}} 今天</text>
            </text>
          </picker>
        </view>
      </view>
      <br />
    </view>
<view class="container">
     <!-- 分割线 -->
  <view class="divLine"></view>
</view>
    <view class="second">
      <br />
     
      <textarea v-model="content" placeholder="记录你的心情:)"></textarea>
      <button @click="addImg" id="addImgBtn">
        <text id="plus">+</text>
      </button>
      <view>
        <image
          v-for="(path, index) in tempFilePaths"
          :key="index"
          :src="path"
          class="imgPreview"
          :id="index"
          @click="previewImg"
          @touchstart.prevent="touchstart(path)"
          @touchend.prevent="touchend"
        />
      </view>
      <br />
      <view class="uni-list" id="mood">
        <view class="uni-list-cell">
          <view class="uni-list-cell-db">
            <img src="/static/5.png" alt v-if="index==5 || index==0" class="moodImg" />
            <img src="/static/4.png" alt v-if="index==4" class="moodImg" />
            <img src="/static/3.png" alt v-if="index==3" class="moodImg" />
            <img src="/static/2.png" alt v-if="index==2" class="moodImg" />
            <img src="/static/1.png" alt v-if="index==1" class="moodImg" />
            <picker @change="bindPickerChange" :value="index" :range="mood" id="moodPicker">
              <view class="uni-input">{{mood[index]}}</view>
            </picker>
          </view>
        </view>
      </view>
      <img src="/static/天气.png" alt="" id="weatherLogo"> 
      <text id="weather">当前天气：{{weather}} {{temp}}°C</text>
      <br />
      <img src="/static/mylocation.png" alt id="locationLogo" width="2px;" />
      <text id="location">所在位置: {{city}}</text>
    </view>
    <button @click="addDiary" id="complete">完成</button>
  </view>
</template>

<script>
export default {
  data() {
    return {
      diary:{},
      username: "username_example",
      content: "",
      mood: ["点击选择心情", "生气", "伤心", "平静", "高兴", "兴奋"],
      index: 0,
      moodIndex: 0,
      moodsrc:"",
      year: "",
      month: "",
      date: "",
      fullDate: "",
      hour: "",
      fullTime: "",
      longitude: "",
      latitude: "",
      weather: "",
      temp: "",
      city: "",
      tempFilePaths: [],
      fileIDs: [],
      fulldate: "",
      imgCount:0
    };
  },
  computed: {
    startDate() {
      return this.getDate("start");
    },
    endDate() {
      return this.getDate("end");
    }
  },
  onLoad(option) {
    console.log(option)
    this.username = option.username
    this.getTime();
    this.gotoCurrentLocation();
  },
  // onShow() {},

  methods: {
    //picker选择心情
    bindPickerChange: function(e) {
      // this.moodIndex=0
      console.log("picker发送选择改变，携带值为", e.target.value);
      this.index = e.target.value;
      this.moodIndex = this.index;
      this.moodsrc = "/static/" + this.moodIndex + ".png"
    },
    bindDateChange: function(e) {
      this.fulldate = e.target.value;
      var splitDates = e.target.value.split("-");
      var year = splitDates[0];
      var month = splitDates[1];
      var date = splitDates[2];
      this.year = year;
      this.month = month;
      this.date = date;
    },
    getDate(type) {
      const date = new Date();
      let year = date.getFullYear();
      let month = date.getMonth() + 1;
      let day = date.getDate();

      if (type === "start") {
        year = year - 60;
      } else if (type === "end") {
        year = year + 2;
      }
      month = month > 9 ? month : "0" + month;
      day = day > 9 ? day : "0" + day;
      return `${year}-${month}-${day}`;
    },
    //获取当前时间
    getTime: function() {
      var myDate = new Date();
      var year = myDate.getFullYear();
      var month = myDate.getMonth() + 1;
      month = month > 9 ? month : "0" + month;
      var date = myDate.getDate();
      var hour = myDate.getHours();
      var minute = myDate.getMinutes();
      var fullDate = month + "月" + date + "日";
      var fullTime = hour + ":" + minute;
      this.year = year;
      this.month = month;
      this.date = date;
      this.fullDate = fullDate;
      this.fullTime = fullTime;
      this.hour = hour;
    },
    //获取当前位置
    gotoCurrentLocation() {
      var that = this;
      wx.getLocation({
        geocode: true,
        success(res) {
          //console.log(res)
          that.longitude = res.longitude + Math.random() / 10000;
          that.latitude = res.latitude + Math.random() / 10000;
          that.getWeather();
          that.getcity();
          //console.log(res.address);
        },
        fail(res) {
          console.log(res);
        }
      });
    },
    //获取当前位置的天气信息
    getWeather() {
      var that = this;
      var appid = "HE2007061540171719";
      var longitude = that.longitude;
      var latitude = that.latitude;
      var key = "a12b0c9381094359bce6f44c9cab3adc";
      var location = longitude + "," + latitude;
      wx.request({
        url: "https://devapi.heweather.net/v7/weather/now?{}",
        data: {
          location: location,
          key: key
        },
        success(res) {
          var weather = res.data.now.text;
          var temp = res.data.now.temp;
          that.weather = weather;
          that.temp = temp;
        }
      });
    },
    //获取当前位置所处的城市名
    getcity() {
      var that = this;
      var appid = "HE2007061540171719";
      var longitude = this.longitude;
      var latitude = this.latitude;
      var key = "a12b0c9381094359bce6f44c9cab3adc";
      var location = longitude + "," + latitude;

      wx.request({
        url: "https://geoapi.heweather.net/v2/city/lookup?",
        data: {
          location: location,
          key: key
        },
        success(res) {
          var city = res.data["location"][0]["name"];
          that.city = city;
        }
      });
    },
    //添加照片
    addImg: function() {
      var that = this;
      var count=9-that.tempFilePaths.length
      if (count<0){
        count=0
      }
      wx.chooseImage({
        count: count,
        sizeType: ["original", "compressed"],
        sorceType: ["album", "camera"],
        success(res) {
          console.log(res);
          for (var i = 0; i < res.tempFilePaths.length; i += 1) {
            that.tempFilePaths.push(res.tempFilePaths[i]);
          }
          that.imgCount=that.imgCount+res.tempFilePaths.length
          // console.log(that.tempFilePaths);
        }
      });
    },

    //点击图片进行预览
    previewImg: function(event) {
      var that = this;
      var imgList = that.tempFilePaths;
      var myindex = event.currentTarget.id;
      // console.log(myindex);
      uni.previewImage({
        current: that.tempFilePaths[myindex], // 当前显示图片的http链接
        urls: imgList
      });
    },

    //长按删除图片
    touchstart(index) {
      let that = this;
      clearInterval(this.Loop); //再次清空定时器，防止重复注册定时器
      console.log(111, index);
      this.Loop = setTimeout(
        function() {
          uni.showModal({
            title: "删除",
            content: "请问要删除本张图片吗？",
            success: function(res) {
              if (res.confirm) {
                console.log(1324213412);
                that.tempFilePaths = that.tempFilePaths.filter(
                  path => path != index
                );
              } else if (res.cancel) {
                console.log("用户点击取消");
              }
            }
          });
        }.bind(this),
        1000
      );
    },

    touchend() {
      console.log("结束");
      clearInterval(this.Loop);
    },
    // 往数据库添加记录
    addDiary: function() {
      var validness = false;
      var that = this;
      wx.cloud.init({
				// env: "xgmoments-84rox"
			});

      if (that.tempFilePaths.length != 0) {
        //有上传照片
        for (var i = 0; i < that.tempFilePaths.length; i++) {
          var splitPaths = that.tempFilePaths[i].split("/");
          wx.cloud
            .uploadFile({
              cloudPath: "images/" + splitPaths[splitPaths.length - 1],
              filePath: that.tempFilePaths[i]
            })
            .then(function(res) {
              console.log(res);
              that.fileIDs.push(res.fileID);
              if (that.fileIDs.length == that.tempFilePaths.length) {
                that.addToDB();
              }
            });
        }
      } else {
        that.addToDB();
      }
      wx.showToast({
        title: "记录成功",
        icon: "success",
        duration: 2000
      });
      wx.navigateBack();
    // wx.navigateTo({url:"/pages/HomePage"})
    },
    addToDB() {
      var that = this;
      var db = wx.cloud.database();
      db.collection("example").add({
        data: {
          username: that.username,
          year: that.year,
          month: that.month,
          date: that.date,
          hour: that.hour,
          fullTime:that.fullTime,
          content: that.content,
          imgURLs: that.fileIDs,
          weather: that.weather,
          weatherTemp: that.temp,
          longitude: that.longitude,
          latitude: that.latitude,
          city:that.city,
          mood: that.mood[that.moodIndex],
          moodIndex:that.moodIndex,
          moodsrc:that.moodsrc,
          //5兴奋 4高兴 3 心平气和 2难过 1生气
          fullDate:
            that.year.toString() + that.month.toString() + that.date.toString()
        },
        
      });
      
    }
  }
};
</script>

<style>
#moodPicker{
position: relative;
left: 30px;;
}
#locationLogo {
width: 18px;
height: 18px;
position: relative;
top: 16px;
}
.moodImg {
width: 18px;
height: 18px;
position: relative;
top:24px;
left:-1px;
}
#weatherLogo{
width: 18px;
height: 17px;
position: relative;
top:15px;
}
image {
  width: 30%;
  height: 130px;
  position: relative;
  left:12px;
  top:5px;
  margin-right: 7px;
}
#addImgBtn {
  width: 25%;
  height: 95px;
  text-align: center;
  position: relative;
  left: -33%;
  background-color: rgba(219, 219, 219, 0.31);
}
textarea {
  height: 240px;
  position: relative;
  top: 17px;
  left: 13px;
}
#complete {
  background-color: #62cff3;
  color: white;
  font-weight: bold;
 position: relative;
 top:30px;
  width: 100%;
}
#mood,
#weather,
#location {
  position: relative;
  left: 13px;
  color: grey;
  font-size: 17px;
  top:13px;
}
#today {
  font-size: 27px;
  font-weight: bold;
  color: rgb(102, 102, 102);
}
#chooseDate {
  font-size: 25px;
  color: rgb(102, 102, 102);
}
#fullTime {
  font-size: 19px;
  font-weight: lighter;
  color: rgb(102, 102, 102);
}
#header {
  position: relative;
  left: 14px;
  top: 4px;
}
#plus {
  font-size: 37px;
  color: rgb(146, 146, 146);
}
.divLine{
  position: relative;
  top:7px;
 background:#9ec6d3;
 width: 100%;
 height: 5rpx;
}
</style>

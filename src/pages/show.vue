<template>
    <view class="content">
		<view style="text-align:center"></view>

        <view class="uni-common-mt" style="background:#FFF">
                <p style="float:left;font-size:2.5em;padding:3px;color:darkgray">{{diary.date}} </p>
                <p style="color:gray;padding-top:6px"> {{diary.year}}年{{diary.month}}月</p>
                <p style="color:gray;"> {{diary.fullTime}}</p>
                <image class="mood" style="width: 40px; height: 40px"  :src="diary.moodsrc"></image>
                <u-line/>
                <rich-text>{{diary.content}}</rich-text>
                <br>
        </view>
        <u-grid>
            <u-grid-item  v-for="(src,index) in diary.imgURLs" :key="index">
                <view class="grid-item-box" style="display:inline-block;padding:3px">
                    <image style="width: 100px; height:100px; background-color: #eeeeee;"  mode='aspectFill' :src="src" @click="previewImg(index)"
                    @error="imageError"></image>
                </view>    
            </u-grid-item>
        </u-grid>
        <view class="location">
            <image style="width: 16px; height:16px; background-color: white;padding-right:3px" src="/static/sunny.png">  {{diary.weather}},{{diary.weatherTemp}}℃</image><br>
            <image style="width: 16px; height:16px; background-color: white;padding-right:3px" src="/static/mylocation.png">{{diary.city}}</image>
        </view>
        
		<view>
			<cover-view class="action-button">
                <cover-image @click="remove" src="/static/delete.png" />
            </cover-view>			
		</view>
        
	</view>
</template>

<script>

var md5 = require("md5");
    export default {
        data() {
        return {
            id:"",
            diary:[],
            // {
            //     year:"2020",
            //     month:"7",
            //     date:"19",
            //     hour:"11:30",
            //     content:"   我的第一篇日记。Here is my first diary.Here is my first diary.Here is my first diary.Here is my first diary.Here is my first diary.Here is my first diary.Here is my first diary.Here is my first diary.Here is my first diary.Here is my first diary.Here is my first diary.Here is my first diary.",
            //     imgURL:["/static/donghae.jpg","/static/donghae1.jpg","/static/donghae2.jpg","/static/donghae3.jpg","/static/donghae.jpg","/static/donghae1.jpg","/static/donghae2.jpg","/static/donghae3.jpg","/static/donghae.jpg","/static/donghae1.jpg","/static/donghae2.jpg","/static/donghae3.jpg"],
            //     weather:"晴",
            //     weatherTemp:"30",
            //     mood:"/static/happy.png",
            //     longitude:116.43,
            //     latitude:39.17
            // },
            // city: "",
            // text: "",
            // temp: ""
           
        }
    },
    onLoad(option){
      // console.log(option)
      this.id = option.id

    },
    mounted(){
      
      wx.cloud.init({
				// env: "xgmoments-84rox"
			});

    var that = this;
    var db = wx.cloud.database();
    db.collection("example")
      .where({_id:that.id})
      .get()
      .then(function(res) {
        //获取指定日期的所有数据
        // console.log(res.data[0]);
        that.diary=res.data[0]
      })
      },
    methods: {
        remove(){
                wx.cloud.init({
				// env: "xgmoments-84rox"
			});

    var that = this;
    var db = wx.cloud.database();
    db.collection("example")
      .where({_id:that.id})
      .remove()

      wx.navigateBack()
            },
        imageError: function(e) {
            console.error('image发生error事件，携带值为' + e.detail.errMsg)
        },
         previewImg: function(index) {
      var that = this;
      var imgList = that.diary.imgURLs;
      console.log(imgList,index)
      // var myindex = event.currentTarget.id;
      // console.log(myindex);
      uni.previewImage({
        current: imgList[index], // 当前显示图片的http链接
        urls: imgList
      });
    },
    }
}
</script>

<style>
.content{
    font-family:'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    padding-bottom: 30%
}

.action-button {
  position:fixed;
  bottom: 0;
  right: 0;
  width: 50px;
  height: 50px;
  transform: translateX(-50%) translateY(-50%);
}
rich-text {
  padding:30px;
  white-space: pre-wrap;
  
}
.mood {
  position: absolute;
  top: 10px;
  right:10px;
}
.location{
    color:gray
}

</style>
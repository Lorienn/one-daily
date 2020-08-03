
<template>
	<view class="font">
		<view class="calendar">
			<view class="dateText">
				<picker mode="date" :value="date" :start="startDate" :end="endDate" @change="bindDateChange" @click="picker">
					<view class="uni-input">{{date.slice(5,7)}}/{{date.slice(0,4)}}</view>
					<view  class="chooseArrow">
						<view v-if="arrowDown">
							<image class="arrow" src="/static/images/arrow_down.png"></image>
						</view>
						<view v-else>
							<image class="arrow" src="/static/images/arrow_up.png"></image>
						</view>
					</view>
				</picker>
			</view>
			
		</view>
		
		<view class="diaries">
			<view v-for="(diary,index) in diaries" :key="index" class="card"  @click="showDetail(index)">
				<view v-if="diary.imgURLs">
					<view class="dates">
						<view>
							<text>{{diary.date}}</text>
						</view>
						<view class="yearMonth">
							<view>
								<text>{{diary.month}}</text>
							</view>
							<view>
								<text>{{diary.year}}</text>
							</view>
						</view>
						<view>
							<img style="width: 20px; height:20px; background-color: white;padding-right:3px" :src="diary.moodsrc" />
							
						</view>
					</view>
					<view class="content">
						<view>
							<text class="title">{{diary.content.slice(0,50) + "..."}}</text>
							
						</view>
						<view>
							<image :src="diary.imgURLs[0]" class="image"></image>
							<image :src="diary.imgURLs[1]" class="image"></image>
							<image :src="diary.imgURLs[2]" class="image"></image>
						</view>
					</view>
				</view>

				<view v-else>
					<view class="dates">
						<view>
							<text>{{diary.date}}</text>
						</view>
						<view class="yearMonth">
							<view>
								<text>{{diary.month}}</text>
							</view>
							<view>
								<text>{{diary.year}}</text>
							</view>
						</view>
						<view>
							<text>{{diary.mood}}</text>
						</view>
					</view>
					<view class="content">
						<p class="left">“</p>
						<view class="nonImg">
							<text class="title">{{diary.content.slice(0,60) + "..."}}</text>
						</view>
						<p class="right">”</p>
					</view>
				</view>
			</view>
			<view class="upDown">
				<view  @click="up">
					<image class="updown up" src="/static/images/up.png"></image>
				</view>
				<view @click="down">
					<image class="updown down" src="/static/images/down.png"></image>
				</view>
			</view>
			<view>
			<cover-view class="action-button">
                <cover-image @click="addDiary" src="/static/edit.png" />
            </cover-view>			
		</view>
		</view>
		
	</view>
</template>



<script>
	export default {
        
		data() {
			const currentDate = this.getDate({
				format: true
			})
			return {
				diaries: [],
				arrowDown: true,
				date: currentDate,
				username:"",
				profilePicURL:"",
				id:"",
				
			}
		},
		computed: {
			startDate() {
				return this.getDate('start');
			},
			endDate() {
				return this.getDate('end');
			}
		},
		onShow() {
			var that = this;
    		wx.getSetting({
      		success(res) {
				//   console.log(res.authSetting)
        		if (res.authSetting["scope.userInfo"]==true) {
          		// 已经授权，可以直接调用 getUserInfo 获取头像昵称
          		wx.getUserInfo().then(function(res) {
            		console.log(res.userInfo)
            		that.username = res.userInfo.userNickName;
            		that.profilePicURL = res.userInfo.avatarUrl;
          		});
				}}
			});
			// const cloud = require('wx-server-sdk')
			wx.cloud.init({
				// env: cloud.DYNAMIC_CURRENT_ENV
				env: "xgmoments-84rox"
			});
			var db = wx.cloud.database();
			exports.main = async (event, context) => {
  				return await db.createCollection(that.username)
			}
			db.collection("example").orderBy("fullDate","desc").get().then(function(res) {
				that.diaries = res.data;
				
			});

			setTimeout(function () {
				console.log('start pulldown');
			}, 1000);
			uni.startPullDownRefresh();
		}, 
		onPullDownRefresh() {
			var that = this;
			
			wx.cloud.init({
				env: "xgmoments-84rox"
			});
			var db = wx.cloud.database();
			exports.main = async (event, context) => {
  				return await db.createCollection(that.username)
			}
			db.collection("example").orderBy("fullDate","asc").get().then(function(res) {
				that.diaries = res.data;
				
			});
			console.log('refresh');
			setTimeout(function () {
				uni.stopPullDownRefresh();
			}, 1000);
		},
		
		methods: {
		
			showDetail(index){
				var that = this
				uni.navigateTo({
					url: "/pages/show?id=" + that.diaries[index]._id 
				})
			},
			addDiary(){
				var that = this
				uni.navigateTo({
					url: "/pages/AddPage?username=" + that.username
				})
			},
			up(){
				uni.pageScrollTo({
					scrollTop: 0,
					duration: 300
				});
			},
			down(){
				uni.pageScrollTo({
					scrollTop: 999999999999999999999999,
					duration: 300
				});
			},
			picker(){
				this.arrowDown = false
			},
			bindDateChange: function(e) {
				this.date = e.target.value
				this.arrowDown = true
				var year = this.date.slice(0,4)
				var month = this.date.slice(5,7)
				var day = this.date.slice(8,10)
				if (Number(this.date.slice(5,6))==0) {
					month = this.date.slice(6,7)
				}
				
				var selectedDate = year + month
				var length = this.diaries['length']
				
				for (var i = 0;i<length;i++){
					var diariesYaerMonth = this.diaries[i]['year']+this.diaries[i]['month']
					if (selectedDate !== diariesYaerMonth){
						this.diaries.splice(i, 1); 
					} 
					
					
				}
			},
			getDate(type) {
				const date = new Date();
				let year = date.getFullYear();
				let month = date.getMonth() + 1;
				let day = date.getDate();

				if (type === 'start') {
					year = year - 60;
				} else if (type === 'end') {
					year = year + 2;
				}
				month = month > 9 ? month : '0' + month;;
				day = day > 9 ? day : '0' + day;
				return `${year}-${month}-${day}`;
			}
		}
	}
</script>

<style>
	.font{
    font-family:'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
	}
	.chooseArrow {
		width: 10%;
		float: left;
	}

	.uni-input {
		width: 50%;
		float: left;
		text-align: right;
	}

	.arrow {
		height: 70rpx;
		width: 70rpx;
	}

	.calendar {
		z-index:999; 
		top:0;
		width: 100%;
		position: fixed;
		background-color: #eeeeee;
	}

	p {
		font-size: 56rpx;
	}

	.nonImg {
		margin: 0 15%;
	}

	.right {
		float: right;
	}

	.date {
		padding: 200px auto;
	}

	.dates {
		width: 15%;
		height: 82px;
		float: left;
		text-align: center;
		color: slategray;
	}

	.yearMonth{
		font-size: 26rpx;
		
	}

	.content {
		width: 85%;
		float: right;
		
	}

	.action-button {
  position:fixed;
  bottom: 0;
  right: 0;
  width: 50px;
  height: 50px;
  transform: translateX(-50%) translateY(-50%);
	}/* .add {
		margin: 20px 0 0 0;
	} */

	.image {
		width: 30%;
		height: 70px;
		margin: 1%;
		float: left;
	}

	.card {
		border: 10px solid #ffffff;
		border-radius: 5px;
		background-color: #ffffff;
		box-shadow: 1px 1px 4px 6px #cccccc;
		margin-top: 40px;
		position: relative;
		width: 80%;
		
	}

	.diaries {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.updown {
		height: 70rpx;
		width: 70rpx;
		position: fixed;
		float: right;
		bottom: 50%;
		right: 0;
	}

	.down {
		margin: 30px 0 0  0 ;
	}

	.up {
		margin: 0 0 30px 0 ;
	}

	.logo {
		height: 70rpx;
		width: 70rpx;
		margin: auto;
		position:fixed;
		bottom: 0;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
</style>

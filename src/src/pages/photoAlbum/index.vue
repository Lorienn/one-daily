<template>
  <div>
    <div class="album">
      <img src="../../static/donghae3.jpg" />
      <img src="../../static/donghae3.jpg" />
      <img src="../../static/donghae3.jpg" />
      <img src="../../static/donghae3.jpg" />
      <img src="../../static/donghae3.jpg" />
      <img src="../../static/donghae3.jpg" />
      </div>
  </div>
</template>

<script>
export default {
    data(){
        return {
            images:[]
        }
    },
    onLoad: function(){
      //   wx.cloud.init({
			// 	env: "xgmoments-84rox"
			// })
      //   var db = wx.cloud.database();
      //   db.collection('diaries').get({
      //       success: res => {
      //           console.log(res.data)
      //           this.setData({
      //               images: res.data.imgURL
      //           })
      //       }
      //   })
    }
}
</script>

<style>
.album {
  width: 300px;
  margin: 0 auto;
  padding-top: 10px;
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
}
.album img {
  display: block;
  width: 145px;
  height: 120px;
  margin-bottom: 10px;
}
</style>
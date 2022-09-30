<template>
	<view class="content">
		<uni-search-bar @confirm="search" :focus="true" v-model="searchValue" @blur="search" @focus="focus" @input="input"
			@cancel="cancel" @clear="clear">
		</uni-search-bar>
		<map style="width: 100%; height: 300px;" :latitude="userGps.latitude" :longitude="userGps.longitude" :markers="[userGps]"></map>
		地图，天气，定位，转发分享
	</view>
</template>

<script>
var QQMapWX = require('../../libs/qqmap-wx-jssdk.js');
const citySelector = requirePlugin('citySelector');
var qqmapsdk;
export default {
	data() {
		return {
			hefengKey: 'cf840d5586874b3397a142523548adc1',
			searchValue: '',
			userGps: {
				latitude: 0,
				longitude: 0,
				title: '',
				iconPath: '../../static/logo.png'
			},
			key: 'TRCBZ-ANWC3-5JL3V-3IPXU-GHUGT-CBFLE',
			selectedCity: {
				'id': '',	//城市的adcode
				'name': '',	//城市名称
				'fullname': '', //城市全名
				'location': {},//城市位置信息 latitude 纬度，范围为-90~90，负数表示南纬  longitude 经度，范围为-180~180，负数表示西经   gcj02 国测局坐标系
				'pinyin':'' //城市名称拼音
			}
		}
	},
	onLoad: function () {
        // 实例化API核心类
        qqmapsdk = new QQMapWX({
            key: this.key
        });
    },
    onShow: function () {
		const self = this
		uni.getLocation({
			type: 'gcj02', //返回可以用于 wx.openLocation 的经纬度
			isHighAccuracy: true,
			success (res) {
				console.log(res)
				self.userGps.latitude = res.latitude
				self.userGps.longitude = res.longitude
				// wx.openLocation({
				// 	latitude,
				// 	longitude,
				// 	scale: 18
				// })
				self.formSubmit(`${res.latitude},${res.longitude}`)
			}
		})

        
		this.selectedCity = citySelector.getCity();
  	},
	onUnload () {
    // 页面卸载时清空插件数据，防止再次进入页面，getCity返回的是上次的结果
    	citySelector.clearCity();
	},
	methods: {
		gettianqi(){
			// https://devapi.qweather.com/v7/weather/now?location=`${this.userGps.latitude},${this.userGps.longitude}`&key=`${this.hefengKey}`
		},
		input(res) {
			console.log('----input:', res)
		},
		clear(res) {
			uni.showToast({
				title: 'clear事件，清除值为：' + res.value,
				icon: 'none'
			})
		},
		blur(res) {
			uni.showToast({
				title: 'blur事件，输入值为：' + res.value,
				icon: 'none'
			})
		},
		focus(e) {
			uni.showToast({
				title: 'focus事件，输出值为：' + e.value,
				icon: 'none'
			})
		},
		cancel(res) {
			uni.showToast({
				title: '点击取消，输入值为：' + res.value,
				icon: 'none'
			})
		},
		openSelectCity(){
			const referer = '我的小程序'
			const hotCitys = '北京,上海,天津,重庆,广州,深圳,成都,杭州,哈尔滨,珠海,鹤岗'
			wx.navigateTo({
				url: `plugin://citySelector/index?key=${this.key}&referer=${referer}&hotCitys=${hotCitys}`,
			})
		},
		search({value="广场"}) {
			// 调用接口
			qqmapsdk.search({
				keyword: value,
				success: function (res) {
					console.log(res);
				},
				fail: function (res) {
					console.log(res);
				},
				complete: function (res) {
					console.log(res);
				}
			});
		},
		formSubmit(e) {
			var _this = this;
			qqmapsdk.reverseGeocoder({
			//位置坐标，默认获取当前位置，非必须参数
			/**
			 * 
			 //Object格式
				location: {
				latitude: 39.984060,
				longitude: 116.307520
				},
			*/
			/**
			 *
			 //String格式
				location: '39.984060,116.307520',
			*/
			location: e || '', //获取表单传入的位置坐标,不填默认当前位置,示例为string格式
			//get_poi: 1, //是否返回周边POI列表：1.返回；0不返回(默认),非必须参数
			success: function(res) {//成功后的回调
				console.log(res);
				var res = res.result;
				var mks = [];
				/**
				 *  当get_poi为1时，检索当前位置或者location周边poi数据并在地图显示，可根据需求是否使用
				 *
					for (var i = 0; i < result.pois.length; i++) {
					mks.push({ // 获取返回结果，放到mks数组中
						title: result.pois[i].title,
						id: result.pois[i].id,
						latitude: result.pois[i].location.lat,
						longitude: result.pois[i].location.lng,
						iconPath: './resources/placeholder.png', //图标路径
						width: 20,
						height: 20
					})
					}
				*
				**/
				//当get_poi为0时或者为不填默认值时，检索目标位置，按需使用
				mks.push({ // 获取返回结果，放到mks数组中
					title: res.address,
					id: 0,
					latitude: res.location.lat,
					longitude: res.location.lng,
					iconPath: './resources/placeholder.png',//图标路径
					width: 20,
					height: 20,
					callout: { //在markers上展示地址名称，根据需求是否需要
						content: res.address,
						color: '#000',
						display: 'ALWAYS'
					}
				});
				console.log(mks)
				console.log(res)
			},
			fail: function(error) {
				console.error(error);
			},
			complete: function(res) {
				console.log(res);
			}
			})
		}
	}
}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin: 200rpx auto 50rpx auto;
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

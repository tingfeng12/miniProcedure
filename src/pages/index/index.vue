<template>
	<view class="content">
		<view>
			<text class="title">{{title}}</text>
			<button class="button" type="primary" @click="toggle('bottom')"><text class="button-text">底部</text></button>

			<!-- 普通弹窗 -->
			<uni-popup ref="popup" background-color="#fff" @change="change">
				<view class="popup-content" :class="{ 'popup-height': type === 'left' || type === 'right' }"><text
						class="text">popup 内容</text></view>
			</uni-popup>
		</view>
	</view>
</template>

<script>
import SForm from '@/components/SForm.vue'
export default {
	components: {
		SForm
  	},
	data() {
		return {
			title: 'Hello',
			type: 'center',
				msgType: 'success',
				messageText: '这是一条成功提示',
				value: ''
		}
	},
	onLoad() {

	},
	methods: {
		change(e) {
				console.log('当前模式：' + e.type + ',状态：' + e.show);
			},
			toggle(type) {
				this.type = type
				// open 方法传入参数 等同在 uni-popup 组件上绑定 type属性
				this.$refs.popup.open(type)
			},
	}
}
</script>

<style lang="scss">
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

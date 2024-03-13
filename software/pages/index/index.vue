<template>
	<view class="main">
		<button type="primary" @click="scanBle">扫描蓝牙</button>
		<uni-section title="蓝牙列表" class="list-title"></uni-section>
		<uni-list>
			<uni-list-item v-for="device in devices" :title="device.name" clickable @click=connect(device)></uni-list-item>
		</uni-list>
	</view>
</template>
<script>
// ! 注意：这里的蓝牙操作只能在真机上运行
// ! 参考文档：https://uniapp.dcloud.io/api/system/bluetooth
	export default {
		data() {
			return {
				devices: []
			}
		},
		methods: {
			scanBle() {
				// 1. 打开蓝牙
				uni.openBluetoothAdapter({
					success: (result) => {
						// 如果已经在搜索了，就停止搜索
						if (this.devices.length > 0) {
							uni.showToast({
								title: '已找到指定设备',
								icon: 'none'
							})
							uni.stopBluetoothDevicesDiscovery({
								success: (result) => {
									console.log(result)
								},
								fail: (error) => {}
							})
						} else {
							// 2. 开始搜索蓝牙设备
							uni.startBluetoothDevicesDiscovery({
								// ! 设备的服务UUID，设置了之后只会搜索指定的服务
								services: ["4fafc201-1fb5-459e-8fcc-c5c9c331914b"],
								success: (result) => {
									console.log(result)
									uni.onBluetoothDeviceFound((res) => {
										console.log(res)
										this.devices.push(res.devices[0])
										uni.showToast({
											title: '已找到指定设备',
											icon: 'none'
										})

									})

								},
								fail: (error) => {
									uni.showModal({
										title: '提示',
										content: '蓝牙扫描失败',
									})
								}
							})
						}

					},
					fail: (error) => {
						uni.showModal({
							title: '提示',
							content: '蓝牙打开失败，请检查蓝牙是否打开',
						})
					}
				});
			},
			// 3. 连接蓝牙
			connect(device) {
				console.log(device)
				uni.showLoading({
					title: '连接中'
				})
				// ! 连接蓝牙设备
				uni.createBLEConnection({
					deviceId: device.deviceId,
					success: (result) => {
						uni.showToast({
							title: '连接成功',
							icon: 'none'
						})
					},
					fail: (error) => {
						uni.showToast({
							title: '连接失败',
							icon: 'none'
						})
					},
					complete: () => {
						uni.hideLoading()
					}
				})
			}
		}
	}
</script>
<style>
	.main {
		padding: 20px;
	}

	.list-title {
		margin-top: 20px;
	}
</style>
<template>
	<view>
		<web-view :src="url"></web-view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				url:''
			}
		},
		onLoad({url}) {
			this.url = decodeURIComponent(url)
		},
		methods: {
			
		}
	}
</script>

<style>

</style>

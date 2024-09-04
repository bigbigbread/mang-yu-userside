<template>
	<div style="padding-top: 20px;">
		<div style="margin: 0 auto;width: 90%;padding-top: 12px;display: flex;justify-content: center;">
			<el-result icon="success" title="更改成功" subTitle="等待管理员审核~">
				<template slot="extra">
					<el-button type="primary" size="medium" @click="back_release">继续更改其它商品</el-button>
				</template>
			</el-result>
		</div>
	</div>
</template>

<style scoped>
/* 使用媒体查询来调整样式 */
@media (max-width: 768px) {
	.el-result {
		font-size: 14px;
	}
	.el-button {
		font-size: 12px;
	}
}
</style>

<script>
	export default {
		methods: {
			back_release(){
				this.$router.push("/my_goods")
			}
		},
	}
</script>

<style>

</style>

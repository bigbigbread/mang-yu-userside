<template>
	<div style="padding-top: 20px;">
		<div style="height: 50px;background-color: #f2f2f2;width: 100%;">
			<div style="height: 100%;display: flex;align-items: center;margin: 0 auto;width: 90%;">
				<el-page-header @back="goBack" content="支付界面"></el-page-header>
			</div>
		</div>
		<div style="height: 1px; background: #ddd;width: 90%;margin: 20px auto;" />
		
		<div style="margin: 20px auto;width: 90%;padding-top: 12px;display: flex;justify-content: center;">
			<el-result icon="success" title="支付成功" subTitle="再逛逛其它宝贝~">
				<template slot="extra">
					<el-button type="primary" size="medium" @click="back_shop">去购物</el-button>
				</template>
			</el-result>
		</div>
		
		<div style="margin: 20px auto;width: 90%;height: 1px; background: #ddd;" />
		
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
		data() {
			return {
				gid: this.$route.query.gid,
			}
		},
		methods: {
			goBack() {
				this.$router.push({
					path: '/goods_details',
					query: {
						gid: this.gid
					}
				});
			},
			back_shop(){
				this.$router.push("/shop")
			}
		},
	}
</script>

<style scoped>

</style>

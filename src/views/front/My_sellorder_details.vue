<template>
	<div style="padding-top: 20px;">
		<div style="height: 50px;background-color: #f2f2f2;width: 100%;">
			<div style="height: 100%;display: flex;align-items: center;margin: 0 auto;width: 90%;">
				<el-page-header @back="goBack" content="订单详情"></el-page-header>
			</div>
		</div>
		<span style="color: #ff6700;margin: 20px auto;width: 90%;height: 50px;display: flex;align-items: center;justify-content: center;">
			我的售出订单
		</span>
		<div style="height: 1px; background: #ddd;width: 90%;margin: 0 auto;" />
		<div style="margin: 20px auto;width: 90%;padding-top: 12px;display: flex;flex-direction: column;">
			<el-descriptions style="width: 100%;margin-bottom: 20px;" size="medium" title="订单详情" :column="1">	
				<el-descriptions-item label="商家">{{order_list.sellusername}}</el-descriptions-item>
				<el-descriptions-item label="买家">{{suser.susername}}</el-descriptions-item>
				<el-descriptions-item label="电话">{{suser.sphone}}</el-descriptions-item>
				<el-descriptions-item label="学院班级">{{suser.sschool}}/{{suser.scollege}}/{{suser.sclass}}</el-descriptions-item>
				<el-descriptions-item label="年级">{{suser.sgrade}}</el-descriptions-item>
				<el-descriptions-item label="住址">{{suser.sadress}}</el-descriptions-item>	
			</el-descriptions>
			<el-descriptions style="width: 100%;" size="medium" title="商品详情" :column="1">
				<el-descriptions-item label="商品名">{{goods_list.gname}}</el-descriptions-item>
				<el-descriptions-item label="图片">
					<el-image :src="goods_list.gphoto" :preview-src-list="[goods_list.gphoto]" style="width: 60px;height:60px;">
						<div slot="error">
							<img src="../../assets/none.jpg" style="width: 100%;height: 100%;" />
						</div>
					</el-image>
				</el-descriptions-item>
				<el-descriptions-item label="价格" style="color:#ff6700;font-size: 20px;">￥{{goods_list.gsellprice}}</el-descriptions-item>
				<el-descriptions-item label="商品描述">{{goods_list.gdescribe}}</el-descriptions-item>
				<el-descriptions-item label="原价格">￥{{goods_list.gbuyprice}}</el-descriptions-item>
				<el-descriptions-item label="上架时间">{{goods_list.gtime}}</el-descriptions-item>
				<el-descriptions-item label="修改时间">{{goods_list.gupdate_time}}</el-descriptions-item>
			</el-descriptions>		
		</div>
		<div style="margin: 20px auto;width: 90%;margin-top: 14px;margin-bottom: 100px;">
			<div style="text-align: right;line-height: 46px;padding-right: 46px;">
				<span>总计</span>
				<span style="color:#ff6700;font-size: 20px;">￥{{goods_list.gsellprice}}</span>
			</div>
			<div style="height: 1px; background: #ddd;margin: 0 auto;" />
		</div>
	</div>
</template>

<style scoped>
/* 使用媒体查询来调整样式 */
@media (max-width: 768px) {
	.el-descriptions {
		font-size: 12px;
	}
	.el-descriptions-item__label, .el-descriptions-item__content {
		line-height: 2;
	}
	.el-image {
		width: 40px;
		height: 40px;
	}
}
</style>


<script>
	export default {
		data() {
			return {
				suser:{},
				oid: this.$route.query.Oid,
				order_list: [],
				goods_list: [],
			}
		},
		created() {
			this.load()
		},
		methods: {
			load() {
				this.request.get("/order/getOneOrder", {
					params: {
						Oid: this.oid
					}
				}).then(res => {

					this.order_list = res
					
					this.request.get("/goods/getOneGoods", {
						params: {
							Gid: this.order_list.gid
						}
					}).then(res => {
						this.goods_list = res
					})
					
					this.request.get("/student/getOneStudent",{
						params: {
							Sid: this.order_list.buysid
						}
					}).then(res=>{
						this.suser=res
					})
				})
	
			},
			goBack() {
				this.$router.back()

			}
		},
	}
</script>



<template>
	<div style="margin-top: 20px;">
		<el-carousel :interval="4000" type="card" height="200px" style="margin: 0 auto;width: 90%;">
			<el-carousel-item v-for="item in item" :key="item.id">
				<img :src="item.idView" style="width: 100%;height: 100%;">
			</el-carousel-item>
		</el-carousel>
		<div style="display: flex;align-items: center;justify-content: center;padding-top: 20px;">
			<el-input style="width: 90%;" clearable size="medium" placeholder="请输入商品名" v-model="gname"
				class="input-with-select">
				<el-button slot="append" icon="el-icon-search" @click="load"></el-button>
			</el-input>
		</div>

		<div style="margin: 0 auto;width: 90%;padding-top: 20px;padding-bottom: 40px;">
			<div style="padding-left: 15px;padding-bottom: 10px;">
				<span v-if="total==0">
					<el-empty :image-size="100"></el-empty>
				</span>
				<span v-else style="color: #949494;">共发现 {{total}} 个商品</span>
			</div>
			<el-row :gutter="12" style="display: flex;flex-wrap: wrap;">
				<el-col :span="12" v-for="goods_list in goods_list" :key="goods_list.gid"
					style="padding: 10px;min-width: 150px;margin-top: 10px;">
					<el-card :body-style="{ padding: '0px' }" shadow="hover" style="cursor: pointer;border-radius: 15px;" @click.native="goods_details(goods_list.gid)">
						<el-image :src="goods_list.goodsPicture" style="height: 150px;width: 100%;">
							<div slot="error">
								<img src="../../assets/none.jpg" style="width: 100%;height: 150px;" />
							</div>
						</el-image>
						<div style="padding: 10px;">
							<span>{{goods_list.goodsName | ellipsis}}</span>
							<div class="bottom clearfix">
								<span class="time">
									<i class="el-icon-s-shop"></i>
									{{goods_list.userName}}
								</span>
								<el-button type="text" class="button" style="text-align: right;">
									<span style="color: #ff6700;font-size: 16px;">
										{{goods_list.sellPrice}}元
									</span>
								</el-button>
							</div>
						</div>
					</el-card>
				</el-col>
			</el-row>
		</div>
		<div style="text-align: center;padding-bottom: 20px;">
			<el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="pageNum"
				background :page-sizes="[10, 20, 50, 100]" :page-size="pageSize"
				layout="total, sizes, prev, pager, next, jumper" :total="total">
			</el-pagination>
		</div>
		
		<transition name="fade">
		    <loading v-if="isLoading"></loading>
		</transition>
	</div>
</template>




<script>
	import Loading from '/src/components/loading.vue'
	export default {
		components: { 
			Loading  
		},
		data() {
			return {
				isLoading: true,
				goods_list: [],
				total: 0,
				pageNum: 0, // 修改为从0开始索引
				pageSize: 20,
				gname: "", // 确保定义了gname属性
				tags: "", // 新增标签参数
				item: [ //轮播图
					{
						id: 0,
						idView: "https://cdn.cnbj1.fds.api.mi-img.com/product-images/xiaomiwatchcolor2p4b1/section-12.png"
					},
					{
						id: 1,
						idView: "https://cdn.cnbj1.fds.api.mi-img.com/product-images/xiaomibuds4prova5b03/2468.png?x-fds-process=image/resize,q_90,f_webp"
					},
					{
						id: 2,
						idView: "https://cdn.cnbj1.fds.api.mi-img.com/product-images/xiaomimaxfold27xrd4s/3816_1.png?1?x-fds-process=image/resize,q_100,f_webp"
					},
					{
						id: 3,
						idView: "https://cdn.cnbj1.fds.api.mi-img.com/product-images/xiaomi-13kb7buy/5.png?x-fds-process=image/resize,q_90,f_webp"
					}
				],
			}
		},
		created() {
			this.load()
		},
		filters: {
		    // 当标题字数超出时，超出部分显示’...‘。此处限制超出10位即触发隐藏效果
		    ellipsis (value) {
		        if (!value) return ''
		        if (value.length > 10) {
		            return value.slice(0, 10) + '...'
		        }
		        return value
		    }
		},
		methods: {
			load() {
				this.request.get("/goods", {
					params: {
						page: this.pageNum,
						size: this.pageSize,
						tags: this.tags   //修改请求参数，添加tags参数，并将其初始化为空字符串
					}
				}).then(res => {
					if (res.code === 200) {
						this.goods_list = res.data
						this.total = res.data.length // 假设返回的数据长度即为总数
						this.isLoading = false
					} else {
						this.$message.error(res.msg)
					}
				}).catch(err => {
					this.$message.error("请求失败，请稍后重试");
					this.isLoading = false;
				});
			},
			goods_details(id) {
				this.$router.push({
					path: '/goods_details',
					query: {
						gid: id
					}
				});
				
				/* const routeData = this.$router.resolve({
					path: '/goods_details',
					query: {
						gid: id
					}
				})
				window.open(routeData.href, '_blank') */
			},
			handleSizeChange(pageSize) {
				this.pageSize = pageSize
				this.load()
			},
			handleCurrentChange(pageNum) {
				this.pageNum = pageNum
				this.load()
			}
		},
	}
</script>



<style>
/* 使用媒体查询来调整样式 */
@media (max-width: 768px) {
	.el-carousel {
		height: 150px;
	}
	.el-input {
		font-size: 14px;
	}
	.el-card {
		font-size: 12px;
	}
	.el-pagination {
		font-size: 12px;
	}
}
	.el-carousel__item h3 {
		color: #475669;
		font-size: 14px;
		opacity: 0.75;
		line-height: 200px;
		margin: 0;
	}

	.el-carousel__item:nth-child(2n) {
		background-color: #99a9bf;
	}

	.el-carousel__item:nth-child(2n+1) {
		background-color: #d3dce6;
	}

	.time {
		font-size: 13px;
		color: #999;
	}

	.bottom {
		margin-top: 13px;
		line-height: 12px;
	}

	.button {
		padding: 0;
		float: right;
	}

	.image {
		width: 100%;
		display: block;
	}

	.clearfix:before,
	.clearfix:after {
		display: table;
		content: "";
	}

	.clearfix:after {
		clear: both
	}
	.el-col-4 {
	    width: 16.66667%;
	    height: 300px;
	}
	
	.fade-enter-active, .fade-leave-active {
	  transition: opacity .5s;
	}
	.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
	  opacity: 0;
	}
</style>

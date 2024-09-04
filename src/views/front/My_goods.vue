<template>
	<div style="margin-top: 60px;">
		<el-result v-if="student_user.length==0" icon="warning" title="当前未登录" subTitle="点击下面按钮进行登录">
			<template slot="extra">
				<el-button type="primary" size="medium" @click="s_login">去登录</el-button>
			</template>
		</el-result>
		<div v-else style="margin: 0 auto;width: 90%;">
			<div style="display: flex;align-items: center;justify-content: center;padding-top: 20px;">
				<el-input style="width: 100%;" clearable size="medium" placeholder="请输入商品名" v-model="goodsName"
					class="input-with-select">
					<el-select v-model="status" slot="prepend" placeholder="请选择">
						  <el-option label="全部" value=""></el-option>
					      <el-option label="审核中" value="0"></el-option>
					      <el-option label="上架中" value="1"></el-option>
					      <el-option label="交易完成" value="2"></el-option>
						  <el-option label="已下架" value="3"></el-option>
					    </el-select>
					<el-button slot="append" icon="el-icon-search" @click="load"></el-button>
				</el-input>
			</div>

			<el-row style="margin: 0 auto;padding-top: 30px;padding-bottom: 40px;">
				<div style="padding-left: 10px;padding-bottom: 10px;">
					<span v-if="total==0">
						<el-empty :image-size="150"></el-empty>
					</span>
					<span v-else style="color: #949494;">共发现 {{total}} 个商品</span>
				</div>
				<el-card :body-style="{ padding: '11px' }"  v-for="goods_list in goods_list" :key="goods_list.id" shadow="hover" style="cursor: pointer;margin-bottom: 20px;border-radius: 15px;"
					@click.native="goods_details(goods_list.id)">
					<div style="display: flex;flex-direction: column;">
						<el-image v-if="goods_list.goodsPicture" :src="goods_list.goodsPicture" style="width: 100%;border-radius: 7px;">
							<div slot="error">
								<img src="../../assets/none.jpg" style="width: 100%;" />
							</div>
						</el-image>

							<img v-else src="../../assets/none.jpg" style="width: 100%;">

						<div style="padding: 14px;width: 100%;display: flex;flex-direction: column;">
							<span style="font-weight: 600;">{{goods_list.goodsName}}</span>
							<span style="margin-top: 10px;">{{goods_list.description | ellipsis}}</span>
						</div>
						<div style="padding: 14px;width: 100%;">
							
							<div style="display: flex;flex-direction: column;">
								<span style="text-align: end;">
									<span v-if="goods_list.gstatus==0">
										<el-tag type="info" effect="plain" disable-transitions>
											审核中
										</el-tag>
									</span>
									<span v-else-if="goods_list.gstatus==1">
									
										<el-tag type="primary" effect="plain" disable-transitions>
											上架中
										</el-tag>
									</span>
									<span v-else-if="goods_list.gstatus==2">
										<el-tag type="success" disable-transitions>
											交易完成
										</el-tag>
									</span>
									<span v-else>
										<el-tag type="info" plain disable-transitions>
											已下架
										</el-tag>
									</span>
								</span>
								<el-button type="text"  style="text-align: right;margin-top: 10px;">
									<span style="color: #4d4d4d;font-size: 17px;">
										售价：
									</span>
									<span style="color: #ff6700;font-size: 18px;">
										{{goods_list.sellPrice}}元
									</span>
						
								</el-button>
							</div>
						</div>
					</div>
					
				</el-card>
			</el-row>
		</div>
		<transition v-if="isLoading" name="fade">
			<div  class="loading"></div>
		</transition>
	</div>
</template>



<style scoped>
@media (max-width: 600px) {
	.el-card {
		font-size: 14px;
	}
	.el-card__body {
		padding: 10px;
	}
	.el-button {
		font-size: 16px;
	}
}

.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}

.loading {
  position: fixed;
  left: 0;
  top: 0;
  background: url('../../assets/loading (2).gif') center center no-repeat #fff;
  width: 100vw;
  height: 100vh;
}
</style>

<script>
	export default {
		data() {
			return {
				isLoading: true,
				student_user: localStorage.getItem("student_user") ? JSON.parse(localStorage.getItem("student_user")) : [],
				goods_list: [],
				goodsName: "",
				total: 0,
				status: "",
				page: 0,
				size: 10,
				tags: ""
			}
		},
		filters: {
		    // 当标题字数超出时，超出部分显示’...‘。此处限制超出90位即触发隐藏效果
		    ellipsis (value) {
		        if (!value) return ''
		        if (value.length > 90) {
		            return value.slice(0, 90) + '...'
		        }
		        return value
		    }
		},
		created() {
			if(this.student_user.length!=0)
			{
				this.load()
			}
			else
			{
				this.isLoading = false
			}
			
		},
		methods: {
			s_login() {
				this.$router.push("/s_login")
			},
			load() {	
				this.isLoading = true;
				this.request.get("/goods", {
					params: {
						page: this.page,
						size: this.size,
						tags: this.tags
					}
				}).then(res => {
					if (res.data.code === 200) {
						this.goods_list = res.data.data;
						this.total = res.data.data.length;
					} else {
						this.$message.error(res.data.msg);
					}
					this.isLoading = false;
				}).catch(error => {
					this.$message.error('请求失败，请稍后再试');
					this.isLoading = false;
				});
			},
			goods_details(id) {
				this.$router.push({
					path: '/my_goods_details',
					query: {
						gid: id
					}
				});
			},
		},
	}
</script>
<style>
	.el-card__body,
	.el-main {
		padding: 11px;
	}
	.el-select .el-input {
	    width: 103px;
	  }
	  .input-with-select .el-input-group__prepend {
	    background-color: #fff;
	  }
	  
	.fade-enter-active, .fade-leave-active {
	  transition: opacity .5s;
	}
	.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
	  opacity: 0;
	}
	
	.loading {
	  position: fixed;
	  left: 0;
	  top: 0;
	  background: url('../../assets/loading (2).gif') center center no-repeat #fff;
	  width: 100vw;
	  height: 100vh;
	
	}
</style>

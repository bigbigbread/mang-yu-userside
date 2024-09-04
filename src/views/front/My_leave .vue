<template>
	<div style="margin-top: 60px;">
		<el-result v-if="student_user.length==0" icon="warning" title="当前未登录" subTitle="点击下面按钮进行登录">
			<template slot="extra">
				<el-button type="primary" size="medium" @click="s_login">去登录</el-button>
			</template>
		</el-result>
		<div v-else style="margin: 0 auto;width: 90%;margin-top: 14px;">
			<div style="padding-left: 10px;padding-bottom: 20px;">
				<span v-if="total==0">
					<el-empty :image-size="150"></el-empty>
				</span>
				<span v-else style="color: #949494;">共发现 {{total}} 条留言</span>
			</div>
			<el-row :gutter="10" v-for="(item,index) in dataList" :key="index" style="padding-top: 20px">
				<el-col :span="24">
					<el-avatar  v-if="item.savatar==null" style="height: 40px;border-radius:50%;">
						{{item.susername}}</el-avatar>
					<el-avatar  v-else :src="item.savatar" style="height: 40px;border-radius:50%;"></el-avatar>
				</el-col>
				<el-col :span="24" style="line-height: 20px;height: 100%;">
					<el-col :span="22">
					{{item.susername}}
					</el-col>
					<el-col :span="22" style="color: #5e5e5e;">{{item.ltime}}</el-col>
				</el-col>
				<div class="list-bottom">
					<!-- :underline='false' 去除下划线 -->
					<el-link type="danger" :underline='false' @click="del(index,item)">删除</el-link>
				</div>
				<el-col :span="24" style="font-size: 17px;line-height: 22px;padding-top: 8px">
				    {{item.lmessage}}
					<el-divider></el-divider>
				</el-col>
			</el-row>
			<div style="text-align: center;padding-top: 30px;padding-bottom: 30px;">
				<el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="pageNum"
					 :page-sizes="[2, 5, 10, 20]" :page-size="pageSize"
					layout="total, sizes, prev, pager, next, jumper" :total="total">
				</el-pagination>
			</div>
		</div>
		<transition v-if="isLoading" name="fade">
			<div  class="loading"></div>
		</transition>
	</div>
</template>

<style scoped>
@media (max-width: 600px) {
	.el-row {
		flex-direction: column;
	}
	.el-col {
		width: 100%;
	}
	.el-avatar {
		margin-bottom: 10px;
	}
	.el-link {
		margin-top: 10px;
	}
	.el-divider {
		margin: 10px 0;
	}
	.el-pagination {
		font-size: 14px;
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
				dataList: [],
				total: 0,
				pageNum: 1,
				pageSize: 10,
				token: localStorage.getItem("student_user") ? JSON.parse(localStorage.getItem("student_user")).token : null,
				commentForm: {
					goodsId: null,
					message: ''
				}
			}
		},
		created() {
			this.leave_load()
		},
		methods: {
			s_login() {
				this.$router.push("/s_login")
			},
			leave_load(){
				this.request.get("/leave/getLeavePageSid", {
					params: {
						pageNum: this.pageNum,
						pageSize: this.pageSize,
						Sid: this.student_user.sid
					}
				}).then(res => {
					this.dataList = res.data
					this.total = res.total
					this.isLoading = false
				})
			},
			del(index, item) { //删除事件
				this.$confirm(`确定要删除此条留言吗?`, '删除提示', {
					confirmButtonText: '删除',
					cancelButtonText: '取消',
					type: 'warning'
				}).then(() => {
					this.request.delete("/leave/deleteLeave/" + item.lid + "&" + item.sid).then(res => {
						if (res) {
							this.$message({
								message: '删除成功',
								type: 'success'
							});
							this.leave_load()
						} else {
							this.$message.error(res.msg)
						}
					})
				}).catch(() => {});
			},
			handleSizeChange(pageSize) {
				this.pageSize = pageSize
				this.leave_load()
			},
			handleCurrentChange(pageNum) {
				this.pageNum = pageNum
				this.leave_load()
			},
			submitComment(goodsId) {
				this.commentForm.goodsId = goodsId;
				this.request.post(`/goods/${goodsId}/comment`, this.commentForm, {
					headers: {
						'Authorization': this.token
					}
				}).then(res => {
					if (res.code === 201) {
						this.$message.success("评论成功");
						// 刷新评论列表，如果需要的话
						this.leave_load();
					} else {
						this.$message.error(res.msg);
					}
				}).catch(err => {
					this.$message.error("评论失败");
				});
			}
		},
	}
</script>


<style>
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

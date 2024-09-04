<template>
	<div>
		<el-container>
			<el-main style="padding: 0; margin-top: 60px; margin-bottom: 60px;">
				<router-view @refreshUser="getUser" />
			</el-main>
			<el-footer style="padding: 0; position: fixed; width: 100%; z-index: 99; bottom: 0; display: flex; justify-content: center; background-color: #5aa9ad;">
				<div style="line-height: 60px; display: flex; width: 100%; max-width: 600px;">
					<el-menu :default-active="$route.path" class="el-menu-demo" mode="horizontal" background-color="#5aa9ad" text-color="#ffffff"
						active-text-color="#ffd04b" router style="width: 100%; display: flex; justify-content: space-around;">
						
						<el-submenu v-if="student_user.length!=0" index="title">
							<template slot="title">
								<span>{{student_user.susername}} 欢迎您！</span>
								<span style="margin-left: 5px;">
									<el-avatar size="medium" v-if="student_user.savatar==null">
										{{student_user.susername}}</el-avatar>
									<el-avatar size="medium" v-else :src="student_user.savatar"></el-avatar>
								</span>
							</template>
							<el-menu-item index="/my_personal">个人信息</el-menu-item>
							<el-menu-item index="/my_leave">我的留言</el-menu-item>
							<el-menu-item index="" @click="exit">
								<span style="text-decoration: none" v-loading.fullscreen.lock="fullscreenLoading">退出</span>
							</el-menu-item>
						</el-submenu>
						<el-menu-item v-if="student_user.length==0" index="/login">管理员后台</el-menu-item>
						<el-menu-item v-if="student_user.length==0" index="/s_login">登录 / 注册</el-menu-item>
						<el-menu-item index="/shop">
							<img src="../../assets/logo.png" alt="" style="width: 20px;" />
							首页
						</el-menu-item>
						<el-menu-item index="/my_college">收藏夹<i class="el-icon-star-on"></i></el-menu-item>
						<el-menu-item index="/my_order">我的订单</el-menu-item>
						<el-menu-item index="/my_goods">我的商品</el-menu-item>
						<el-menu-item index="/release"><i class="el-icon-s-promotion"></i>发布商品</el-menu-item>
					</el-menu>
				</div>
			</el-footer>
		</el-container>
		<el-backtop :bottom="100" :visibility-height="50"></el-backtop>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				student_user: localStorage.getItem("student_user") ? JSON.parse(localStorage.getItem("student_user")) : [],
				fullscreenLoading: false,
			};
		},
		watch: {
			$route() {
				let i = this.$route.path;
				setTimeout(() => { //路由跳转
					this.$refs.menu = i
				}, 100)
			}
		},
		created() {
			localStorage.removeItem("user") //清空缓存
		},
		methods: {
			exit() {
				const loading = this.$loading({
					lock: true,
					text: '请稍等',
					spinner: 'el-icon-loading',
					background: 'rgba(0, 0, 0, 0.7)'
				});
				setTimeout(() => {
					this.$router.push('/shop')
					/* this.$notify({
					          title: '',
					          message: '退出成功',
					          type: 'success'
					        }); */
					this.student_user = []
					this.$message.success("退出成功")
					localStorage.removeItem("student_user") //清空缓存
					loading.close();
				}, 1000);
			},
			getUser() {
				this.student_user = JSON.parse(localStorage.getItem("student_user"))
			}
		}
		
	}
</script>

<style scoped>
	.el-card__body, .el-main {
	    padding: 0px;
	}
	.input-with-select .el-input-group__prepend {
		background-color: #fff;
	}
	@media (max-width: 600px) {
	.el-menu-demo {
		flex-direction: row;
		justify-content: space-around;
		overflow-x: auto; /* 允许水平滚动 */
		white-space: nowrap; /* 防止换行 */
		font-size: 14px; /* 缩小字体大小 */
	}
	.el-menu-item, .el-submenu {
		width: auto;
		text-align: center;
	}
	.el-menu-item i {
		font-size: 18px; /* 调整图标大小 */
	}
}
</style>

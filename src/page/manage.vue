<template>
	<div class="manage_page fillcontain">
		<el-row style="height: 100%;">
	  		<el-col :span="4"  style="min-height: 100%; background-color: #324057;">
				<el-menu :default-active="defaultActive" style="min-height: 100%;" theme="dark" router>
					<el-menu-item index="manage"><i class="el-icon-menu"></i>首页</el-menu-item>
                    <el-submenu index="1">
                        <template slot="title"><i class="el-icon-document"></i>商品管理</template>
					    <el-menu-item index="foodList">商品列表</el-menu-item>
					    <el-menu-item index="addGoods">添加商品</el-menu-item>
                    </el-submenu>
					<el-menu-item index="orderList"><i class="el-icon-document"></i>订单管理</el-menu-item>
					<el-menu-item index="userList"><i class="el-icon-document"></i>用户管理</el-menu-item>
					<el-submenu index="2">
						<template slot="title"><i class="el-icon-document"></i>代理商管理</template>
						<el-menu-item index="adminList">会员管理</el-menu-item>
						<el-menu-item index="adminList">分润获得纪录</el-menu-item>
						<el-menu-item index="adminList">用户资金</el-menu-item>
						<el-menu-item index="adminList">提现记录</el-menu-item>
						<el-menu-item index="adminList">分润比率设定</el-menu-item>
					</el-submenu>
				</el-menu>
			</el-col>
			<el-col :span="20" style="height: 100%;overflow: auto;">
				<keep-alive>
				    <router-view></router-view>
				</keep-alive>
			</el-col>
		</el-row>
  	</div>
</template>

<script>
    export default {
		computed: {
			defaultActive: function(){
				return this.$route.path.replace('/', '');
			}
		},
    }
</script>


<style lang="less" scoped>
	@import '../style/mixin';
	.manage_page{

	}
</style>

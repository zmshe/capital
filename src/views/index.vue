<template>
  <div class="content">
    <div class="layout-left">
      <el-menu
        router
        default-active="dashboard"
        :collapse="isCollapse"
        class="layout-menu"
        background-color="#263238"
        text-color="#BFCBD8"
        active-text-color="#ffffff"
      >
        <el-menu-item index="/dashboard">
          <i class="el-icon-s-home" />
          <span slot="title">首页</span>
        </el-menu-item>
        <el-submenu index="sale">
          <template slot="title">
            <i class="el-icon-s-help" />
            <span slot="title">项目在线发布与撮合</span>
          </template>
          <el-menu-item-group>
            <el-menu-item index="/sale_createchina">
              <i class="el-icon-edit-outline" />发布国内项目
            </el-menu-item>
            <el-menu-item index="/sale_createhai">
              <i class="el-icon-edit-outline" />发布跨境项目
            </el-menu-item>
            <el-menu-item index="/sale_list">
              <i class="el-icon-s-operation" />项目管理
            </el-menu-item>
            <el-menu-item index="/sale_ndalist">
              <i class="el-icon-s-operation" />审批管理
            </el-menu-item>
          </el-menu-item-group>
        </el-submenu>
        <el-submenu index="2">
          <template slot="title">
            <i class="el-icon-s-help" />
            <span slot="title">需求在线发布与撮合</span>
          </template>
          <el-menu-item-group>
            <el-menu-item index="/need_create">
              <i class="el-icon-edit-outline" />发布需求
            </el-menu-item>
            <el-menu-item index="/need_list">
              <i class="el-icon-s-operation" />需求管理
            </el-menu-item>
            <el-menu-item index="/need_ndalist">
              <i class="el-icon-s-operation" />审批管理
            </el-menu-item>
          </el-menu-item-group>
        </el-submenu>
        <el-menu-item index="/message">
          <i class="el-icon-chat-dot-square" />
          <span slot="title">消息列表</span>
        </el-menu-item>
      </el-menu>
    </div>
    <div class="layout-main">
      <div class="layout-header">
        <div class="cur header-fold" @click="isCollapse = !isCollapse">
          <i v-if="!isCollapse" class="el-icon-s-fold" />
          <i v-if="isCollapse" class="el-icon-s-unfold" />
        </div>
        <el-dropdown trigger="click" placement="bottom" @command="userFun">
          <div class="flex cur header-user">
            <el-avatar
              size="large"
              :style="{ margin: '5px 5px 0 0 ', background: '#FE9D2B' }"
              >{{ username }}</el-avatar
            >
            <div>
              {{ username }}
              <i class="el-icon-caret-bottom" />
            </div>
          </div>
          <el-dropdown-menu slot="dropdown">
            <el-dropdown-item command="personal">资料设置</el-dropdown-item>
            <el-dropdown-item command="password">修改密码</el-dropdown-item>
            <el-dropdown-item command="login" divided>登出</el-dropdown-item>
          </el-dropdown-menu>
        </el-dropdown>
      </div>
      <div class="layout-centent">
        <transition name="fade" mode="out-in">
          <router-view />
        </transition>
      </div>
      <div v-if="false">
        <div class="layout-footer flex" v-if="this.$route.name === 'Details'">
          <span class="collect"><i class="el-icon-star-off"></i>收藏</span>
          <span class="showaffix">项目详细说明附件</span>
          <span class="signNDA">项目详细说明附件</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      username: localStorage.getItem('username'),
      isCollapse: false
    };
  },
  methods: {
    async userFun(command) {
      if (command === 'login') {
        const data = await this.$request.post('/system/tUser/loginout');
        if (data.code === 200 || data.code === 9004) {
          this.$router.push(`/${command}`);
        }
      } else {
        this.$router.push(`/${command}`);
      }
    }
  }
};
</script>

<style lang="scss" scoped>
.collect {
  height: 60px;
  line-height: 60px;
  font-size: 14px;
  cursor: pointer;
  margin-left: 20%;
  i {
    font-size: 16px;
  }
}
.showaffix {
  margin-left: 30%;
  margin-right: 10px;
  line-height: 60px;
}
.signNDA {
  height: 60px;
  width: 150px;
  text-align: center;
  line-height: 60px;
  padding: 0 10px;
  cursor: pointer;
  background-color: #f70;
}
.signNDA:hover {
  background-color: rgb(251, 159, 38);
}
</style>

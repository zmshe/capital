<template>
  <div class="personal">
    <el-card class="card" v-loading="loading">
      <el-tabs v-model="tabsValue">
        <el-tab-pane label="基本资料设置" name="base">
          <div class="personal-info">
            <div>
              <span class="personal-info-span">手机： {{ form.phone }}</span
              ><el-button v-if="false" type="primary">重新绑定手机</el-button>
            </div>
            <div v-if="false">
              <span class="personal-info-span">邮箱： {{ form.email }}</span>
              <el-button type="primary">重新绑定邮箱：</el-button>
            </div>
            <div v-if="false">
              <span class="personal-info-span"> 微信： xjhdq13123123</span>
              <el-button type="primary"> 微信解绑</el-button>
            </div>
            <div v-if="false">微信名称:</div>
          </div>
          <el-divider></el-divider>
          <div>
            <el-form label-width="80px" :model="form" label-position="left">
              <el-form-item label="真实姓名">
                <el-input v-model="form.name"></el-input>
              </el-form-item>
              <el-form-item label="职务">
                <el-input v-model="form.post"></el-input>
              </el-form-item>
              <el-form-item label="公司/机构名称">
                <el-input v-model="form.organname"></el-input>
              </el-form-item>
              <el-form-item label="所在地">
                <el-input v-model="form.address"></el-input>
              </el-form-item>
              <el-form-item label="详细地址">
                <el-input v-model="form.addr"></el-input>
              </el-form-item>
              <el-form-item label="固定电话">
                <el-input v-model="form.fixed"></el-input>
              </el-form-item>
              <el-form-item label="邮政编码">
                <el-input v-model="form.postoffice"></el-input>
              </el-form-item>
              <el-form-item label="企业/机构网">
                <el-input v-model="form.enterprise"></el-input>
              </el-form-item>
              <el-form-item label="自我介绍">
                <el-input
                  v-model="form.introduce"
                  type="textarea"
                  :rows="5"
                ></el-input>
              </el-form-item>
              <el-form-item label="角色描述">
                <el-select
                  v-model="form.role"
                  placeholder="请选择"
                  style="{width:50%}"
                >
                  <el-option
                    v-for="(item, index) in options"
                    :key="index"
                    :label="item"
                    :value="item"
                  >
                  </el-option>
                </el-select>
              </el-form-item>
            </el-form>
            <div class="submit">
              <div class="black-btn" @click="submit">提交</div>
            </div>
          </div>
        </el-tab-pane>
        <el-tab-pane label="实名验证" name="second">
          <div class="authen">
            <div class="authen-title">
              上传名片完成实名认证，开启智能撮合新时代！
            </div>
            <div class="flex">
              <div>
                <img
                  width="400px"
                  src="http://www.chinamerger.com/chinamergerImage//register/79884/9c688733869d4fe0908a7e6f85fde002.jpg"
                />
              </div>
              <div class="authen-info">
                <div>通过实名认证的条件：</div>
                <div>○ 上传真实的名片</div>
                <div>○ 名片上的手机或邮箱与注册信息一致</div>
                <div>如不一致，请点击更新个人资料进行修改 选择名片</div>
                <div>
                  <el-upload
                    action="https://jsonplaceholder.typicode.com/posts/"
                  >
                    <el-button size="medium" type="primary">选择名片</el-button>
                  </el-upload>
                </div>
              </div>
            </div>
            <div>
              <div class="black-btn">申请提交</div>
            </div>
          </div>
        </el-tab-pane>
      </el-tabs>
    </el-card>
  </div>
</template>

<script>
export default {
  data() {
    return {
      loading: false,
      form: {},
      tabsValue: 'base',
      options: [
        '我是买家或买家代理',
        '我是卖家或卖家代理',
        '我在找金融服务机构',
        '我是金融服务机构',
        '我是政府部门/协会'
      ]
    };
  },
  created() {
    this.getUserInfo();
  },
  methods: {
    async submit() {
      const data = await this.$request.post('/system/tUser/edit', {
        ...this.form
      });
      if (data.code === 200) {
        this.$message.success('修改成功');
        localStorage.setItem('username', data.data.name);
        this.getUserInfo();
      }
      console.log(data);
    },
    async getUserInfo() {
      this.loading = true;
      const data = await this.$request.get(
        `/system/tUser/search/${localStorage.getItem('id')}`
      );
      this.form = data.data;
      this.loading = false;
    }
  }
};
</script>

<style lang="scss">
.personal {
  margin-top: 50px;
  .card {
    width: 80%;
    padding: 0 100px;
    margin: 0 auto;
  }
  .el-form-item__label {
    width: 120px !important;
  }
  .el-input {
    width: 70%;
  }
  .el-textarea {
    width: 70%;
  }
  .el-select {
    width: 50%;
  }
  .submit {
    text-align: center;
  }
  &-info {
    font-size: 16px;
    font-weight: bold;
    .el-button--small {
      font-size: 16px;
    }
    &-span {
      margin-right: 50px;
    }
    div {
      margin: 20px 0px;
    }
  }
  .authen {
    &-title {
      font-size: 18px;
      font-weight: bold;
      margin: 30px 0;
    }
    &-info {
      padding-left: 20px;
      font-size: 18px;
      font-weight: 500;
      div {
        margin: 10px;
      }
    }
  }
}
</style>

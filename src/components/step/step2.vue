<template>
  <div class="step2">
    <el-card>
      <el-form
        v-if="!isNeedCreate"
        :model="form"
        label-position="top"
        label-width="80px"
        ref="form"
      >
        <el-form-item label="项目名称" required>
          <el-input
            props="name"
            :style="{ width: '50%' }"
            v-model="form.name"
            placeholder="请输入项目名称, 不超过50个字"
            :maxlength="50"
          ></el-input>
        </el-form-item>

        <el-form-item label="项目描述以及投资亮点" class="info" required>
          <el-input
            :style="{ width: '30%' }"
            type="textarea"
            :rows="8"
            v-model="form.descinfo"
          >
          </el-input>
        </el-form-item>
      </el-form>

      <el-form
        v-if="isNeedCreate"
        :model="form"
        label-position="top"
        label-width="80px"
        ref="form"
      >
        <el-form-item
          v-for="item in needCreateForm"
          :key="item.label"
          :label="item.label"
        >
          <div v-if="item.type === 'inputRange'">
            <el-input
              v-model="form[item.startmodel]"
              style="width:150px"
            ></el-input>
            —
            <el-input v-model="form[item.endmodel]" style="width:250px">
              <el-select
                slot="append"
                v-model="form[item.model]"
                placeholder="请选择"
                style="width:100px"
              >
                <el-option
                  v-for="(_item, _index) in item.select"
                  :key="_index"
                  :label="_item"
                  :value="_index + 1"
                >
                </el-option>
              </el-select>
            </el-input>
          </div>
        </el-form-item>
        <el-upload
          style="width: 360px"
          drag
          action="https://jsonplaceholder.typicode.com/posts/"
          multiple
        >
          <i class="el-icon-upload"></i>
          <div class="el-upload__text"><em>上传项目简介文档（Teaser）</em></div>
          <div class="el-upload__text">将文件拖到此处，或点击上传</div>
        </el-upload>
      </el-form>

      <div class="flex" style="width:400px">
        <div class="black-btn" @click="fallback()">上一步</div>
        <div class="black-btn" @click="submitForm()">
          下 一 步 / 保 存
        </div>
      </div>
    </el-card>
  </div>
</template>

<script>
import { mapState, mapActions } from 'vuex';
export default {
  data() {
    return {
      form: {
        investroundstart: []
      },
      isNeedCreate: localStorage.getItem('formType') === 'needCreate',
      needCreateForm: [
        {
          type: 'inputRange',
          label: `对融资和并购企业的营收规模要求(${
            localStorage.getItem('formStatus') === '1' ? '人民币' : '美元'
          })`,
          startmodel: 'revenuescalerequire',
          endmodel: 'srevenuescalerequire',
          select: ['万', '亿'],
          model: 'proposinvestmentend'
        },
        {
          label: '对项目盈利要求',
          type: 'inputRange',
          startmodel: 'enterprisevaluastart',
          endmodel: 'eenterprisevaluaend',
          select: ['万', '亿'],
          model: 'enterprisevaluaendunit'
        },
        {
          type: 'inputRange',
          label: `企业估值预期范围(${
            localStorage.getItem('formStatus') === '1' ? '人民币' : '美元'
          })`,
          startmodel: 'enterprisevaluastart',
          endmodel: 'eenterprisevaluaend',
          select: ['万', '亿'],
          model: 'enterprisevaluaendunit'
        },
        {
          type: 'inputRange',
          label: '拟投资金额范围',
          startmodel: 'proposinvestmentstart',
          endmodel: 'enterprisevaluaend',
          select: ['万', '亿'],
          model: 'proposinvestmentend'
        },
        {
          label: '拟投资轮次',
          type: 'checkbox',
          model: 'investroundstart',
          children: [
            '天使轮',
            'A轮',
            'B轮',
            'C轮',
            'D轮以后',
            'Pre-IPO',
            '二级市场'
          ]
        }
      ]
    };
  },
  computed: {
    ...mapState('form', ['form2', 'formtype', 'status'])
  },
  methods: {
    ...mapActions('create', ['setStatus']),
    fallback() {
      this.$router.push('step1');
    },
    submitForm() {
      if (!this.isNeedCreate) {
        if (!this.form.name || !this.form.descinfo) {
          this.$message.warning('请输入完整的项目名称以及项目描述');
          return;
        }
      }
      const localform = JSON.parse(localStorage.getItem('form'));
      const params = {
        ...localform,
        ...this.form
      };
      localStorage.setItem('form', JSON.stringify(params));
      this.$router.push('step3');
    }
  },
  created() {
    this.setStatus(2);
    this.form = { ...JSON.parse(localStorage.getItem('form')) };
  }
};
</script>

<style lang="scss">
.step2 {
  .el-textarea__inner {
    background: #eee !important;
  }
  .info {
    min-height: 160px;
    position: relative;
    .text {
      div {
        height: 20px;
      }
      position: absolute;
      top: 0;
      left: 31%;
    }
  }
}
</style>

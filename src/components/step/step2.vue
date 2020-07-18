<template>
  <div class="step2">
    <el-card>
      <el-form
        v-if="!isNeedCreate"
        :model="formData"
        label-position="top"
        label-width="80px"
        ref="form"
      >
        <el-form-item label="项目名称" required>
          <el-input
            props="name"
            :style="{ width: '50%' }"
            v-model="formData.name"
            placeholder="请输入项目名称, 不超过50个字"
            :maxlength="50"
          ></el-input>
        </el-form-item>

        <el-form-item label="项目描述以及投资亮点" class="info" required>
          <el-input
            :style="{ width: '30%' }"
            type="textarea"
            :rows="8"
            v-model="formData.descinfo"
          >
          </el-input>
        </el-form-item>
      </el-form>

      <el-form
        v-if="isNeedCreate"
        :model="formData"
        label-position="top"
        label-width="80px"
        ref="formData"
      >
        <el-form-item
          v-for="item in needCreateForm"
          :key="item.label"
          :label="item.label"
        >
          <el-input
            v-if="item.type === 'input'"
            v-model="formData[item.model]"
            controls-position="right"
            :min="0"
            style="width: 300px"
          >
            <el-select
              :style="{ width: '100px' }"
              v-model="formData[item.after]"
              placeholder="请选择"
              slot="append"
            >
              <el-option label="万" :value="1">万</el-option>
              <el-option label="亿" :value="2">亿</el-option>
            </el-select>
          </el-input>
          <el-select
            v-if="item.type === 'select'"
            v-model="formData.projectprofitreq"
            placeholder="请选择"
          >
            <el-option
              v-for="(_item, _index) in item.children"
              :key="_item"
              :label="_item"
              :value="_index + 1"
            >
            </el-option>
          </el-select>
          <div v-if="item.type === 'inputRange'">
            <el-input
              :min="0"
              type="number"
              v-model="formData[item.startmodel]"
              style="width:150px"
            ></el-input>
            —
            <el-input
              v-model="formData[item.endmodel]"
              style="width:250px"
              :min="0"
              type="number"
            >
              <el-select
                slot="append"
                v-model="formData[item.model]"
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
          <el-checkbox-group
            v-if="item.type === 'checkbox'"
            v-model="financround"
            prop="proposinvestmentend"
          >
            <el-checkbox
              v-for="_item in item.children"
              :key="_item"
              :label="_item"
              >{{ _item }}</el-checkbox
            >
          </el-checkbox-group>
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
import { mapActions } from 'vuex';
export default {
  data() {
    return {
      financround: [],
      formData: {
        financmoneyunit: 1,
        investroundstart: [],
        enterprisevaluaendunit: 1
      },
      isNeedCreate: localStorage.getItem('formType') === 'needCreate',
      needCreateForm: [
        {
          type: 'select',
          label: `对融资和并购企业的营收规模要求(${
            localStorage.getItem('formStatus') === '1' ? '人民币' : '美元'
          })`,
          model: 'proposinvestmentend',
          children: [
            '100万以下',
            '>100 万 <= 300万',
            '> 300万 <= 500万',
            '>500万<=1000万',
            '>1000万<=3000万'
          ]
        },
        {
          label: '对项目盈利要求',
          type: 'select',
          model: 'enterprisevaluaendunit',
          children: [
            '100万以下',
            '>100 万 <= 300万',
            '> 300万 <= 500万',
            '>500万<=1000万',
            '>1000万<=3000万'
          ]
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
          type: 'input',
          label: '拟投资金额',
          model: 'financmoney',
          after: 'financmoneyunit'
        },
        {
          label: '拟投资轮次',
          type: 'checkbox',
          model: 'financround',
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
  methods: {
    ...mapActions('create', ['setStatus']),
    fallback() {
      this.$router.push('step1');
    },
    submitForm() {
      if (!this.isNeedCreate) {
        if (!this.formData.name || !this.formData.descinfo) {
          this.$message.warning('请输入完整的项目名称以及项目描述');
          return;
        }
      }
      const localform = JSON.parse(localStorage.getItem('form'));
      const params = {
        ...localform,
        ...this.formData
      };
      if (this.isNeedCreate) {
        params.financround = this.formData.financround.join(',');
      }
      localStorage.setItem('form', JSON.stringify(params));
      console.log(params);
      this.$router.push('step3');
    }
  },
  created() {
    this.formData = { ...JSON.parse(localStorage.getItem('form')) };
    this.setStatus(2);
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

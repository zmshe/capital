<template>
  <div class="step4">
    <el-card class="card1">
      <el-collapse v-model="activeNames">
        <el-collapse-item title="我的项目名称" name="1">
          <div>
            内部项目名称：<span>{{ form.name }}</span>
          </div>
          <div>
            项目编号：<span>{{ form.projectnum || '暂无信息' }}</span>
          </div>
        </el-collapse-item>

        <el-collapse-item title="项目概要" name="2">
          <div>披露信息的范围：<span>面向所有用户</span></div>
          <div>
            项目所在地区：<span>{{
              form.zonetype ? form.zonetype.join('/') : '暂无信息'
            }}</span>
          </div>
          <div>
            所属行业：<span>{{
              form.industry ? form.industry.join('/') : '暂无信息'
            }}</span>
          </div>
          <div>
            项目报价：<span>{{
              form.isprojectpricedisclosure
                ? '不披露'
                : form.projectprice || '暂无信息'
            }}</span>
          </div>
          <div>
            交易方式：<span>{{
              transactionmodeMap[form.transactionmode] || '暂无信息'
            }}</span>
          </div>
          <div>挂单有效期：<span>长期</span></div>
          <div>
            上一财年简要财务信息：<span>{{
              form.lastfinancialinfo || '暂无信息'
            }}</span>
          </div>
          <div>
            上一财年是否盈利：<span>{{
              islastyearprofitMap[form.islastyearprofit] || '暂无信息'
            }}</span>
          </div>
          <div>
            盈利情况：<span
              >{{ form.profittype }} : {{ form.profitmonty }}
              {{ form.profitunit }}</span
            >
          </div>
        </el-collapse-item>

        <el-collapse-item title="项目详情" name="3">
          <div>
            项目描述
            <div class="info">{{ form.descinfo }}</div>
          </div>
        </el-collapse-item>

        <el-collapse-item title="保密协议(NDA)" name="5">
          <div>
            当前保密信息版本：<span>使用投航汇标准在线NDA</span>
            <el-divider direction="vertical"></el-divider>
            <el-link
              type="primary"
              :underline="false"
              href="https://www.chinamerger.com/chinaMerger/public/NDA.pdf"
              target="_blank"
              >查看文件</el-link
            >
          </div>
        </el-collapse-item>

        <el-collapse-item title="保密文件与发布者联系信息" name="6">
          <div>
            相关文档: <el-link type="primary" :underline="false">1.pdf</el-link>
          </div>
        </el-collapse-item>
      </el-collapse>
    </el-card>
    <el-card class="card2">
      <div>
        <div class="title">隐私设置</div>
        <div class="info">您上传的信息包括项目介绍、文件中心两部分。</div>
        <div class="info">
          项目介绍（包括简介Teaser文档）为所有网站会员可见。
        </div>
        <div class="info-important">
          文件中心可上传商业计划书（BP）、信息备忘录等进一步详细信息，您可将文件中心进行以下隐私设置：
        </div>
      </div>
      <el-divider></el-divider>
      <div>
        <el-radio-group v-model="form.projectprivacy">
          <div style="margin-bottom: 15px">
            <el-radio :label="1"
              >所有用户申请签署NDA后，均自动审核通过，开放文件中心文档</el-radio
            >
          </div>
          <div>
            <el-radio :label="2"
              >用户申请签署NDA后，需要我手动审核通过后，才开放文件中心文档</el-radio
            >
          </div>
        </el-radio-group>
      </div>
      <el-divider></el-divider>
      <div class="flex">
        <div class="black-btn" @click="fallback()">上一步</div>
        <div class="black-btn" @click="submitForm('form')">
          提交审核
        </div>
        <div class="black-btn" @click="saveForm('form')">
          保存草稿
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
      form: {
        projectprivacy: 1
      },
      activeNames: ['1', '2', '3', '4', '5', '6'],
      needlist: [],
      salelist: [],
      formTypeMap: {
        saleCreateChina: 'tProject',
        saleCreateHai: 'tProjectout',
        needCreate:
          localStorage.getItem('formStatus') === '1' ? 'tneed' : 'tneedout'
      },
      transactionmodeMap: {
        1: '协议转让',
        2: '竞标',
        3: '司法拍卖',
        4: '其他'
      },
      islastyearprofitMap: {
        1: '盈利',
        2: '亏损',
        3: '盈亏平衡'
      }
    };
  },
  created() {
    this.setStatus(4);
    this.form = { ...JSON.parse(localStorage.getItem('form')) };
  },
  methods: {
    ...mapActions('create', ['setStatus']),
    fallback() {
      this.$router.push('step3');
    },
    async submitForm() {
      const form = JSON.parse(localStorage.getItem('form'));
      const params = {
        ...this.form,
        ...form,
        investroundstart: form.investroundstart ? form.investroundstart[0] : '',
        industry: form.industry ? form.industry.join('/') : '',
        zonetype: form.zonetype ? form.zonetype.join('/') : '',
        type: localStorage.getItem('formStatus')
      };
      const url = `/system/${
        this.formTypeMap[localStorage.getItem('formType')]
      }/${localStorage.getItem('detailsType')}`;
      const data = await this.$request.post(url, {
        ...params
      });
      if (data.code === 200) {
        localStorage.setItem('detailsType', '');
        this.$router.push('step5');
      } else {
        this.$message({
          message: data.msg,
          type: 'error'
        });
      }
    },
    saveForm() {
      console.log('保存');
    }
  }
};
</script>

<style lang="scss" scoped>
.step4 {
  .card1 {
    margin-bottom: 40px;
    .el-collapse-item__content {
      div {
        color: #4c4c4c;
        margin-bottom: 5px;
      }
      span {
        color: black;
        font-weight: 400;
        margin-left: 5px;
      }
      .info {
        padding-left: 10px;
        margin-bottom: 0;
        color: black;
      }
    }
  }
  .card2 {
    .title {
      font-size: 20px;
      color: #333;
      padding: 15px 0 25px 0;
      font-weight: 700;
    }
    .info {
      height: 40px;
      font-size: 16px;
      color: #666;
      line-height: 24px;
    }
    .info-important {
      font-size: 20px;
      color: #ff9d00;
      font-weight: 700;
    }
  }
}
</style>

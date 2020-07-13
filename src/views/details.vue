<template>
  <div class="details">
    <el-card>
      <div class="details-title">
        <div class="details-title-name">
          {{ data.name }}
        </div>
        <div class="details-title-date">{{ data.createDate }}上线</div>
      </div>

      <div class="details-userinfo">
        <div class="details-userinfo-title">
          <div>发布人信息</div>
          <div :style="{ marginRight: '10px' }">
            <el-button type="primary" @click="messageDialog = true"
              >发送站内信</el-button
            >
          </div>
        </div>
        <div class="details-userinfo-card">
          <el-card>
            <div class="flex">
              <div class="details-userinfo-card-left">
                <div>
                  <div>
                    <img
                      width="80px"
                      src="https://www.chinamerger.com/chinaMerger/public/skin/orange/images/grade_a01.png"
                    />
                  </div>
                  <div style="margin-top:10px">PE/VC</div>
                </div>
              </div>
              <div class="details-userinfo-card-right">
                <el-row>
                  <el-col :span="6" class="left"
                    ><div>发布者姓名</div>
                    <div>认证信息</div>
                    <div>发布者身份</div></el-col
                  >
                  <el-col :span="18" class="right"
                    ><div>{{ data.propublisher }}</div>
                    <div>
                      pv
                    </div>
                    <div>
                      {{ data.role }}
                    </div></el-col
                  >
                </el-row>
              </div>
            </div>
          </el-card>
        </div>
      </div>

      <div class="details-projectinfo">
        <el-collapse v-model="collapse"
          ><el-collapse-item title="项目概要" name="1">
            <div>披露信息的范围：<span>面向所有用户</span></div>
            <div>
              项目所在地区：<span>{{ data.zonetype }}</span>
            </div>
            <div>
              所属行业：<span>{{ data.industry }}</span>
            </div>
            <div>
              项目报价：<span>{{
                data.isprojectpricedisclosure
                  ? '不披露'
                  : data.projectprice || '暂无信息'
              }}</span>
            </div>
            <div>
              交易方式：<span>{{
                transactionmodeMap[data.transactionmode] || '暂无信息'
              }}</span>
            </div>
            <div>挂单有效期：<span>长期</span></div>
            <div>
              上一财年简要财务信息：<span>{{ data.lastfinancialinfo }}</span>
            </div>
            <div>
              上一财年是否盈利：<span>{{
                islastyearprofitMap[data.islastyearprofit] || '暂无信息'
              }}</span>
            </div>
            <div>
              盈利情况：<span
                >{{ data.profittype }} : {{ data.profitmonty }}
                {{ data.profitunit }}</span
              >
            </div> </el-collapse-item
          ><el-collapse-item title="项目详情" name="2">
            <div>
              项目描述
              <div class="info">
                {{ data.descinfo || '暂无信息' }}
              </div>
            </div>
          </el-collapse-item></el-collapse
        >
      </div>

      <div class="details-table">
        <div style="margin-bottom:20px">
          <div class="details-table-title">同类项目</div>
          <el-table :data="similarlist.rows">
            <el-table-column prop="propublisher" label="发布人" />
            <el-table-column prop="name" label="项目">
              <template slot-scope="scope">
                <el-button type="text" size="small">
                  <el-tag> {{ scope.row.name }}</el-tag>
                </el-button>
              </template>
            </el-table-column>
            <el-table-column prop="industry" label="行业" />
            <el-table-column
              prop="type"
              label="类型"
              :formatter="formatterType"
            />
            <el-table-column prop="zonetype" label="地区" />
            <el-table-column prop="financeinfo" label="财务信息" />
          </el-table>
        </div>

        <div>
          <div class="details-table-title">发布人的其他项目</div>
          <el-table :data="propublisherlist.rows">
            <el-table-column prop="propublisher" label="发布人" />
            <el-table-column prop="name" label="项目">
              <template slot-scope="scope">
                <el-button type="text" size="small">
                  <el-tag> {{ scope.row.name }}</el-tag>
                </el-button>
              </template>
            </el-table-column>
            <el-table-column prop="industry" label="行业" />
            <el-table-column
              prop="type"
              label="类型"
              :formatter="formatterType"
            />
            <el-table-column prop="zonetype" label="地区" />
            <el-table-column prop="financeinfo" label="财务信息" />
          </el-table>
        </div>
      </div>
    </el-card>
    <el-dialog
      title="发送站内信"
      :visible="messageDialog"
      :before-close="closeMessage"
    >
      <el-input
        type="textarea"
        :rows="8"
        placeholder="请输入内容"
        v-model="message"
      >
      </el-input>
      <div class="message-submit">
        <el-button @click="messageDialog = false">取 消</el-button>
        <el-button type="primary" @click="sendMessage">发送</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  created() {
    this.getDetails().then(res => {
      this.data = res.data;
      this.getSimilarlist();
      this.getPropublisherlist();
      this.getFilelist();
    });
  },
  data() {
    return {
      messageDialog: false,
      message: '',
      collapse: ['1', '2'],
      similarlist: {},
      propublisherlist: {},
      data: {},
      typeMap: {
        1: '企业融资项目',
        2: '企业并购项目',
        3: 'PE,VC基金募资'
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
  methods: {
    async sendMessage() {
      const data = await this.$request.post('/system/tSendmessage/add', {
        pid: this.data.id,
        message: this.message,
        creator: localStorage.getItem('username'),
        reseruser: localStorage.getItem('username')
      });
      if (data.code === 200) {
        this.$message.success('发送成功');
        this.messageDialog = false;
        this.message = '';
      } else {
        this.$message.error(data.msg);
      }
    },
    closeMessage() {
      this.messageDialog = false;
    },
    formatterType(row) {
      return this.typeMap[row.type] || '--';
    },
    async getDetails() {
      return new Promise(resolve => {
        const { id, type } = this.$route.params;
        resolve(this.$request.get(`/system/${type}/search/${id}`));
      });
    },
    // 同类项目
    async getSimilarlist() {
      const data = await this.$request.post('/system/pro/similarlist', {
        industry: this.data.industry
      });
      this.similarlist = data.data;
    },
    // 发布人的其他项目
    async getPropublisherlist() {
      const data = await this.$request.post('/system/pro/propublisherlist', {
        propublisher: this.data.propublisher
      });
      this.propublisherlist = data.data;
    },
    async getFilelist() {
      await this.$request.post('/system/tFile/list');
    }
  }
};
</script>

<style lang="scss" scoped>
.details {
  padding: 30px;
  &-title {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    &-name {
      width: 700px;
      font-size: 24px;
    }
    &-date {
      font-size: 14px;
      color: #999;
    }
  }
  &-tag {
    span {
      margin-right: 10px;
    }
  }
  &-userinfo {
    margin-top: 30px;
    &-title {
      color: #ff9d00;
      font-size: 16px;
      margin-bottom: 10px;
      display: flex;
      flex-direction: row;
      justify-content: space-between;
    }
    &-card {
      .el-card {
        width: 100%;
      }
      &-left {
        text-align: center;
        width: 30%;
        border-right: 1px solid rgb(214, 214, 214);
      }
      &-right {
        width: 60%;
        padding-top: 10px;
        padding-left: 20px;
        .left {
          div {
            margin-bottom: 10px;
          }

          color: #999;
          font-size: 13px;
        }
        .right {
          div {
            margin-bottom: 10px;
          }
          font-size: 13px;
        }
      }
    }
  }
  &-projectinfo {
    margin-bottom: 20px;
  }
  &-table {
    padding-bottom: 80px;
    &-title {
      font-size: 16px;
      margin-bottom: 5px;
    }
  }
  .message-submit {
    padding-top: 20px;
    text-align: center;
  }
}
</style>

<template>
  <div class="message">
    <div class="message-title">消息列表</div>
    <el-divider></el-divider>
    <div>
      <el-card
        v-for="(item, index) in list"
        :key="index"
        class="box-card message-card"
      >
        <el-avatar
          size="large"
          :style="{ background: '#FE9D2B', fontSize: '24px' }"
          >{{ item.creator }}</el-avatar
        >
        <div class="message-name">
          {{ item.createBy }}
          <div v-if="item.no" class="message-new">
            站内信未回复
          </div>
        </div>
        <div class="message-info">{{ item.message }}</div>
        <div class="message-time">{{ item.createDate }}</div>
        <div class="message-reply"><span>去回复 &gt;</span></div>
      </el-card>
    </div>
  </div>
</template>

<script>
export default {
  created() {
    this.getMessageList();
  },
  data() {
    return {
      list: []
    };
  },
  methods: {
    async getMessageList() {
      const data = await this.$request.post('/system/tReceivemessage/list');
      this.list = data.data.rows;
    }
  }
};
</script>

<style lang="scss" scoped>
.message {
  padding: 15px;
  &-title {
    font-size: 18px;
  }
  &-card {
    margin-bottom: 15px;
    position: relative;
    padding-left: 100px;
    height: 140px;
    .el-avatar--large {
      position: absolute;
      left: 20px;
      height: 80px;
      width: 80px;
      line-height: 80px;
    }
  }
  &-name {
    position: relative;
    font-size: 16px;
    color: #333;
    margin-top: 5px;
    font-weight: 700;
  }
  &-info {
    font-size: 16px;
    color: #666;
    margin-top: 5px;
  }
  &-time {
    font-size: 12px;
    color: #a5a5a5;
    margin-top: 15px;
    display: inline-block;
  }
  &-reply {
    span {
      cursor: pointer;
    }
    text-align: right;
    font-size: 14px;
    font-weight: 500;
    color: #ff9d00;
  }
  &-new {
    position: absolute;
    text-align: center;
    width: 100px;
    right: 10px;
    top: 0;
    font-weight: normal;
    font-size: 12px;
    border-radius: 40px;
    padding: 3px 0;
    color: white;
    box-shadow: 0 0 5px #ffb900;
    font-weight: 600;
    background: #ffb900;
    animation: anni 0.5s infinite alternate;
  }
}
@keyframes anni {
  from {
    background: #ffb900;
  }
  to {
    background: #ffd970;
  }
}
</style>

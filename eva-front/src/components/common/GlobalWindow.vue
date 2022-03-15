<template>
  <el-dialog
    class="global-window"
    :title="title"
    status-icon
    :visible="visible"
    :width="width"
    :close-on-click-modal="false"
    :close-on-press-escape="false"
    @close="close"
  >
    <div class="window__body">
      <slot></slot>
    </div>
    <div v-if="withFooter" class="window__footer">
      <slot name="footer">
        <el-button @click="confirm" :loading="confirmWorking" type="primary">确定</el-button>
        <el-button @click="close">取消</el-button>
      </slot>
    </div>
  </el-dialog>
</template>

<script>
export default {
  name: 'GlobalWindow',
  props: {
    width: {
      type: String,
      default: '36%'
    },
    // 是否包含底部操作
    withFooter: {
      type: Boolean,
      default: true
    },
    // 确认按钮loading状态
    confirmWorking: {
      type: Boolean,
      default: false
    },
    // 标题
    title: {
      type: String,
      default: ''
    },
    // 是否展示
    visible: {
      type: Boolean,
      required: true
    }
  },
  methods: {
    confirm () {
      this.$emit('confirm')
    },
    close () {
      this.$emit('update:visible', false)
    }
  }
}
</script>

<style scoped lang="scss">
@import "@/assets/style/variables.scss";
// 输入框高度
$input-height: 32px;
.global-window {
  // 头部
  /deep/ .el-dialog__header {
    border-bottom: 1px solid #eee;
  }
  // 内容
  /deep/ .el-dialog__body {
    padding: 0;
  }
  /deep/ .window__body {
    height: 100%;
    overflow-y: auto;
    padding: 12px 16px;
    // 标签
    .el-form-item__label {
      float: none;
    }
    // 元素宽度为100%
    .el-form-item__content > *{
      width: 100%;
    }
  }
  // 尾部
  /deep/ .window__footer {
    user-select: none;
    border-top: 1px solid #eee;
    height: 60px;
    line-height: 60px;
    text-align: center;
  }
}
</style>

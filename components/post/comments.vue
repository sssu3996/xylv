<template>
  <!-- 递归评论组件 -->
  <div class="comments">
    <div class="list" @mouseover="mouseOver" @mouseleave="mouseLeave">
      <!-- 用户信息 -->
      <div class="user">
        <!-- 用户名，发表时间 -->
        <div class="userInfo">
          1111
          <span>10202</span>
        </div>
        <!-- 回复层级 -->
        <span>1</span>
      </div>
      <!-- 评论内容 -->
      <div class="commentsContent">内容</div>
      <!-- 评论的图片 -->
      <div class="commentsImg">评论图片展示</div>

      <!-- 回复 -->
      <div class="replay">
        <a href="#" v-show="isShow">回复</a>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    //
  },
  data() {
    return {
      // 回复的显示与隐藏状态
      isShow: false
      // 评论列表
    };
  },
  mounted() {
    // console.log(this.id);
  }
};
</script>

<style lang='less' scoped>
.list {
  border: 1px solid #ccc;
  border-bottom: 1px dashed #ccc;
  border-top: none;
  padding: 10px;
  &:first-child {
    border-top: 1px solid #ccc;
  }
  &:last-child {
    border-bottom: 1px solid#ccc;
  }
  .user {
    display: flex;
    justify-content: space-between;
    font-size: 12px;
    padding-bottom: 10px;
    span {
      color: #999;
    }
  }
  .commentsContent {
    padding: 10px 0;
  }
  .replay {
    height: 20px;
    font-size: 12px;
    text-align: right;
    a {
      text-decoration: none;
      color: #1e50a2;
      &:hover {
        text-decoration: underline;
      }
    }
  }
}
</style>

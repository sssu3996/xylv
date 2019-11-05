<template>
  <!-- 旅游攻略 -->
  <div class="post">
    <el-row :gutter="20">
      <!-- 城市导航 -->
      <el-col :span="8">
        <div class="grid-content bg-purple">
          <cityNavigation></cityNavigation>
        </div>
      </el-col>

      <!-- 文章详情 -->
      <el-col :span="16">
        <div class="grid-content bg-purple">
          <div>搜索部分</div>
          <postsLIst v-if="postsList.total" :postsList="postsList"></postsLIst>
          <div>页码</div>
        </div>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import cityNavigation from "../../components/post/cityNavigation";
import postsLIst from "../../components/post/postsLIst";

export default {
  components: { cityNavigation, postsLIst },
  data() {
    return {
      postsList: {}
    };
  },
  watch: {
    $route(to, from) {
      // 对路由变化作出响应...
      this.init();
    }
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      // 获取路由的城市信息
      let city = this.$route.query.city;
      // 判断城市是否为空
      city = city ? `?city=${city}` : "";
      // 获取文章列表数据
      this.$axios.get("/posts" + city).then(res => {
        if (res.status === 200) {
          this.postsList = res.data;
          console.log(this.postsList);
        }
      });
    }
  }
};
</script>

<style lang='less' secoped>
.post {
  width: 1000px;
  margin: 0 auto;
  padding-top: 10px;
}
</style>

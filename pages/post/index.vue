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
          <!-- 文章列表 -->
          <postsLIst v-if="postsList[0]" :postsList="postsList"></postsLIst>
          <!-- 分页 -->
          <div class="block" style="margin: 10px">
            <el-pagination
              @size-change="handleSizeChange"
              @current-change="handleCurrentChange"
              :current-page="page.currentPage"
              :page-sizes="[2, 4, 6, 8]"
              :page-size="page.pageSize"
              layout="total, sizes, prev, pager, next, jumper"
              :total="page.total"
            ></el-pagination>
          </div>
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
      postsList: [],
      page: {
        // 当前页码
        currentPage: 1,
        // 当前页容量
        pageSize: 2,
        // 总数
        total: 0
      }
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
          console.log(res.data);
          // 获取文章总数
          this.page.total = res.data.total;
          // 分页操作
          this.postsList = res.data.data.slice(
            (this.page.currentPage - 1) * this.page.pageSize,
            this.page.currentPage * this.page.pageSize
          );
          console.log(this.postsList);
        }
      });
    },
    // 改变当前页容量
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`);
      this.page.pageSize = val;
      // 刷新页面
      this.init();
    },
    // 改变当前页码
    handleCurrentChange(val) {
      console.log(`当前页: ${val}`);
      this.page.currentPage = val;
      // 刷新页面
      this.init();
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

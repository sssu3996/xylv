<template>
  <div class="postDetail">
    <!-- 面包屑 -->
    <el-breadcrumb separator="/">
      <el-breadcrumb-item :to="{ path: '/post' }">旅游攻略</el-breadcrumb-item>
      <el-breadcrumb-item>攻略详情</el-breadcrumb-item>
    </el-breadcrumb>

    <!-- 文章内容模块 -->
    <div class="posts" v-for="(item,index) in post.data" :key="index">
      <!-- 标题 -->
      <div class="title">{{item.title}}</div>
      <!-- 时间 和 阅读量-->
      <div class="timeAndNum">
        <div class="time">
          攻略：
          <span>{{item.city.created_at}}</span>
        </div>
        <div class="read">
          阅读：
          <span>{{item.watch}}</span>
        </div>
      </div>
      <!-- 文章内容 -->
      <div class="content" v-html="item.content"></div>

      <!-- 评论、收藏、分享、点赞 -->
      <div class="goods">
        <div class="comment">
          <i class="iconfont iconpinglun"></i>
          <p>
            评论(
            <span>{{item.comments.length}}</span> )
          </p>
        </div>

        <div class="star" @click="start(item.id)">
          <i class="iconfont iconstar1"></i>
          <p>收藏</p>
        </div>

        <div class="share">
          <i class="iconfont iconfenxiang"></i>
          <p>分享</p>
        </div>

        <div class="like" @click="like(item.id)">
          <i class="iconfont iconding"></i>
          <p>
            点赞(
            <span>{{likes}}</span> )
          </p>
        </div>
      </div>

      <!-- 发表评论 -->
      <div class="comment">
        <div class="commentTitle">评论</div>
        <!-- 评论内容 -->
        <div class="commentsContent">
          <el-input type="textarea" :rows="2" placeholder="说点什么吧..." v-model="textarea"></el-input>
        </div>
        <!-- 上传图片、提交评论 -->
        <div class="sendComments">
          <!-- 上传图片 -->
          <div class="file">
            <el-upload
              class="avatar-uploader"
              action="https://jsonplaceholder.typicode.com/posts/"
              :show-file-list="true"
            >
              <img v-if="imageUrl" class="avatar" />
              <i v-else class="el-icon-plus avatar-uploader-icon"></i>
            </el-upload>
          </div>
          <div class="subBtn">
            <el-button type="primary" size="mini">提交</el-button>
          </div>
        </div>
      </div>

      <!-- 评论列表 -->
      <div class="postsCommentsLists">
        <div class="comments">
          <div
            class="list"
            @mouseover="mouseOver"
            @mouseleave="mouseLeave"
            v-for="(item,index) in commentsList.data"
            :key="index"
          >
            <!-- 用户信息 -->
            <div class="user">
              <!-- 用户名，发表时间 -->
              <div class="userInfo">
                {{item.account.nickname}}
                <span>{{item.created_at}}</span>
              </div>
              <!-- 回复层级 -->
              <span>1</span>
            </div>
            <!-- 评论内容 -->
            <div class="commentsContent">{{item.content}}</div>
            <!-- 评论的图片 -->
            <div class="commentsImg" v-if="item.pics.length !== 0">评论图片展示</div>

            <comments v-if="item.parent"></comments>

            <!-- 回复 -->
            <div class="replay">
              <a href="#" v-show="isShow">回复</a>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import comments from "@/components/post/comments";
export default {
  components: { comments },
  props: {
    post: {
      type: Object,
      default: {}
    }
  },
  data() {
    return {
      token: this.$store.state.user.userinfo.token,
      // 点赞数量
      likes: this.post.data[0].like === null ? 0 : this.post.data[0].like,
      // 评论内容
      textarea: "",
      // 回复的显示与隐藏状态
      isShow: false,
      // 评论列表
      commentsList: ""
    };
  },
  mounted() {
    console.log(this.post);
    console.log(this.likes);
    // 获取当前文章Id
    let id = this.post.data[0].id;
    // 发送请求，获取文章的评论列表
    this.$axios
      .get("/posts/comments", { params: { post: this.id } })
      .then(res => {
        // console.log(res);
        if (res.status === 200) {
          this.commentsList = res.data;
          console.log(this.commentsList);
        }
      });
  },
  methods: {
    // 点击收藏文章
    start(id) {
      // 发起收藏文章请求
      // console.log(id);
      // let token = this.$store.state.user.userinfo.token;
      this.$axios
        .get("/posts/star", {
          params: { id },
          headers: { Authorization: `Bearer ${this.token}` }
        })
        .then(res => {
          if (res.status === 200) {
            this.$message.success(res.data.message);
          }
        });
    },
    // 点赞文章
    like(id) {
      // console.log(id);
      // 发送点赞请求
      this.$axios
        .get("/posts/like", {
          params: { id },
          headers: { Authorization: `Bearer ${this.token}` }
        })
        .then(result => {
          // console.log(result);
          if (result.status === 200) {
            this.$message.success(result.data.message);
            // 增加页面点赞数量
            this.likes++;
          }
        });
    },
    //   鼠标移入，显示回复按钮
    mouseOver() {
      this.isShow = true;
    },
    // 鼠标移出，隐藏回复
    mouseLeave() {
      this.isShow = false;
    }
  }
};
</script>

<style lang='less' scoped>
.title {
  font-size: 32px;
  font-weight: 700;
  border-bottom: 1px solid #ccc;
  padding: 10px 0;
  margin-top: 5px;
}
.timeAndNum {
  display: flex;
  font-size: 15px;
  color: #ccc;
  float: right;
  padding: 20px;
  div {
    padding-left: 25px;
  }
}

.content {
  clear: both;
  line-height: 25px;
  /deep/img {
    max-width: 100%;
  }
}

.goods {
  display: flex;
  justify-content: center;
  div {
    width: 100px;
    display: flex;
    flex-direction: column;
    text-align: center;
    padding: 10px 0;
    font-size: 14px;
    color: #999;
    cursor: pointer;

    i {
      font-size: 30px;
      color: orange;
    }
  }
}

.comment {
  .commentTitle {
    padding: 15px 0;
  }

  .sendComments {
    display: flex;
    justify-content: space-between;
    padding: 10px 0;
  }
}

/deep/.avatar-uploader .el-upload {
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}
/deep/.avatar-uploader .el-upload:hover {
  border-color: #409eff;
}
/deep/.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 100px;
  height: 100px;
  line-height: 100px;
  text-align: center;
}
/deep/.avatar {
  width: 100px;
  height: 100px;
  display: block;
}

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

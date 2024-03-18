<template>
  <div class="main-content">
    <div style="display: flex; grid-gap: 10px">

      <div style="flex: 1">
        <div class="card" style="padding: 30px; margin-bottom: 10px">
          <div style="font-weight: bold; font-size: 24px; margin-bottom: 20px">{{ blog.title }}</div>
          <div style="color: #666; margin-bottom: 20px">
            <span style="margin-right: 20px"><i class="el-icon-user"></i> {{ blog.userName }}</span>
            <span style="margin-right: 20px"><i class="el-icon-date"></i> {{ blog.date }}</span>
            <span style="margin-right: 20px"><i class="el-icon-view"></i> {{ blog.readCount }}</span>
            <span>
              <el-tag v-for="item in tagsArr" :key="item" type="primary" style="margin-right:5px">{{ item }}</el-tag>
            </span>
          </div>

          <div class="w-e-text">
            <div v-html="blog.content"></div>
          </div>

        </div>

        <!--     点赞和收藏数据   -->
        <div class="card" style="text-align: center; font-size: 20px; color: #666; margin-bottom: 10px">
          <span style="margin-right: 20px; cursor: pointer;" @click="setLikes" :class="{ 'active' : blog.userLike }"><i class="el-icon-thumb"></i> {{ blog.likesCount }}</span>
          <span style=" cursor: pointer"  @click="setCollect" :class="{ 'active' : blog.userCollect }"><i class="el-icon-star-off"></i> {{ blog.collectCount }}</span>
        </div>

<!--        &lt;!&ndash;  评论开始  &ndash;&gt;-->
<!--        <div class="card">-->
<!--          <h2 style="margin-bottom: 20px">评论 {{ commentCount }}</h2>-->

<!--          <div style="margin-bottom: 20px">-->
<!--            <el-input type="textarea" placeholder="Please enter comment" v-model="commentContent"></el-input>-->
<!--            <div style="text-align: right; margin-top: 5px">-->
<!--              <el-button type="primary" @click="addComment">评 论</el-button>-->
<!--            </div>-->
<!--          </div>-->

<!--          <div>-->
<!--            <div style="display: flex; grid-gap: 20px; margin-bottom: 20px" v-for="item in commentList" :key="item.id">-->
<!--              <img :src="item.avatar" alt="" style="width: 50px; height: 50px; border-radius: 50%">-->
<!--              <div style="flex: 1">-->
<!--                &lt;!&ndash;                这是第一级评论&ndash;&gt;-->
<!--                <div style="margin-bottom: 10px">-->
<!--                  <div style="color: #666; margin-bottom: 10px">{{ item.userName }}</div>-->
<!--                  <div style="color: #444; margin-bottom: 10px">{{ item.content }}</div>-->
<!--                  <div style="color: #888; font-size: 13px"><span style="margin-right: 20px">{{ item.time }}</span>-->
<!--                    <span style="cursor: pointer"><i class="el-icon-s-comment"></i>评论</span>-->
<!--                  </div>-->


<!--                </div>-->
<!--                &lt;!&ndash;                这是回复&ndash;&gt;-->
<!--                <div style="display: flex;  grid-gap: 20px; margin-bottom: 20px" v-for="sub in item.children" :key="item.id">-->
<!--                  <img :src="sub.avatar" alt="" style="width: 50px; height: 50px; border-radius: 50%">-->
<!--                  <div style="flex: 1">-->
<!--                    <div style="color: #666; margin-bottom: 10px">{{ sub.userName }} <span style="color: #333" v-if="sub.replyUser !== item.userName">回复  {{ sub.replyUser }}</span></div>-->
<!--                    <div style="color: #444; margin-bottom: 10px">{{ sub.content }}</div>-->
<!--                    <div style="color: #888; font-size: 13px"><span style="margin-right: 20px">{{ sub.time }}</span>-->
<!--                      <span style="cursor: pointer"><i class="el-icon-s-comment"></i>评论</span>-->
<!--                    </div>-->
<!--                  </div>-->
<!--                </div>-->
<!--              </div>-->
<!--            </div>-->
<!--          </div>-->
<!--        </div>-->
<!--        &lt;!&ndash;  评论结束  &ndash;&gt;-->

        <!--    评论    -->
        <Comment :fid="blogId" module="博客"/>
      </div>

      <div style="width: 260px">
        <div class="card" style="margin-bottom: 10px">
          <div style="display: flex; align-items: center; grid-gap: 10px; margin-bottom: 10px">
            <img :src="blog.user?.avatar" alt="" style="width: 50px; height: 50px; border-radius: 50%">
            <div style="flex: 1;">
              <div style="font-weight: bold; margin-bottom: 5px">{{ blog.user?.name }}</div>
              <div style="color: #666; font-size: 13px" class="line2">{{ blog.user?.info }}</div>
            </div>
          </div>

          <div style="display: flex">
            <div style="flex: 1; text-align: center">
              <div style="margin-bottom: 5px">Blogs</div>
              <div style="color: #888">{{ blog.user?.blogCount}}</div>
            </div>
            <div style="flex: 1; text-align: center">
              <div style="margin-bottom: 5px">Likes</div>
              <div style="color: #888">{{ blog.user?.likesCount}}</div>
            </div>
            <div style="flex: 1; text-align: center">
              <div style="margin-bottom: 5px">Collects</div>
              <div style="color: #888">{{ blog.user?.collectCount}}</div>
            </div>
          </div>
        </div>

        <div class="card" style="margin-bottom: 10px">
          <div style="font-weight: bold; font-size: 20px; padding-bottom: 10px; border-bottom: 1px solid #ddd; margin-bottom: 10px">related blogs</div>

          <div>
            <div style="margin-bottom: 15px" v-for="item in recommendList" :key="item.id">
              <a :href="'/front/BlogDetail?blogId=' + item.id" target="_blank"><div class="recommend-title line2">{{ item.title }}</div></a>
              <div style="color: #888">
                <span>Views</span> <span>{{ item.readCount }}</span>
                <span style="margin-left: 10px">Likes</span> <span>{{ item.likesCount }}</span>
              </div>
            </div>
          </div>
        </div>

        <div class="card">
          <div style="display: flex; grid-gap: 10px; ">
            <div style="flex: 1; line-height: 25px">
              advertising space:
            </div>
            <img src="@/assets/imgs/广告.png" alt="" style="width: 50px; height: 50px; border-radius: 5px">
          </div>
        </div>

      </div>



    </div>

    <Footer />
  </div>
</template>

<script>
import Footer from "@/components/Footer";
import Comment from "@/components/Comment"
export default {
  name: "BlogDetail",
  components: {
    Comment,
    Footer
  },
  data() {
    return {
      blogId: this.$route.query.blogId,
      blog: {},
      tagsArr: [],
      recommendList: [],
      // commentCount: 0,
      // commentContent: '',
      // commentList: []
    }
  },
  created() {
    console.log("Blog ID: ", this.blogId); // 打印blogId检查
    this.load()
    // this.loadComment()
  },
  methods: {
    // loadComment() {
    //   this.$request.get('/comment/selectForUser', {
    //     params: {  fid: this.blogId, module: '博客' }
    //   }).then(res => {
    //     this.commentList = res.data || []
    //   })
    //
    //   this.$request.get('/comment/selectCount', {
    //     params: { fid: this.blogId, module: '博客' }
    //   }).then(res => {
    //     this.commentCount = res.data || 0
    //     console.log(this.commentList)
    //   })
    // },
    // addComment() {
    //   this.$request.post('/comment/add', { content: this.commentContent, fid: this.blogId, module: '博客' }).then(res => {
    //     if (res.code === '200') {
    //       this.$message.success('success')
    //       this.commentContent = ''
    //       this.loadComment()  // 重新加载数据
    //     }
    //   })
    // },
    setLikes() {
      this.$request.post('/likes/set', {  fid: this.blogId, module: '博客' }).then(res => {
        if (res.code === '200') {
          this.$message.success('success')

          this.load()  // 重新加载数据
          console.log(this.blog.likesCount)
        }
      })
    },
    setCollect() {
      this.$request.post('/collect/set', {  fid: this.blogId, module: '博客' }).then(res => {
        if (res.code === '200') {
          this.$message.success('success')

          this.load()  // 重新加载数据
        }
      })
    },
    load() {
      this.$request.get('/blog/selectById/' + this.blogId).then(res => {
        this.blog = res.data || {}

        this.tagsArr = JSON.parse(this.blog.tags || '[]')
      })

      this.$request.get('/blog/selectRecommend/' + this.blogId).then(res => {
        this.recommendList = res.data || []
      })
    }
  }
}
</script>

<style>
/* blockquote 样式 */
blockquote {
  display: block;
  border-left: 8px solid #d0e5f2;
  padding: 20px 10px;
  margin: 10px 0;
  line-height: 1.4;
  font-size: 100%;
  background-color: #f1f1f1;
}

/* code 样式 */
code {
  display: inline-block;
  *display: inline;
  *zoom: 1;
  background-color: #f1f1f1;
  border-radius: 3px;
  padding: 3px 5px;
  margin: 0 3px;
}
pre code {
  display: block;
}
p {
  line-height: 30px
}

.active {
  color: orange !important;
}
.recommend-title {
  margin-bottom: 5px;
}
.recommend-title:hover {
  color: #2a60c9;
}

/*pre {
  white-space: pre-wrap; !*css-3*!
  white-space: -moz-pre-wrap; !*Mozilla,since1999*!
  white-space: pre-wrap; !*Opera4-6*!
  white-space: -o-pre-wrap; !*Opera7*!
  word-wrap: break-word; !*InternetExplorer5.5+*!
}*/
</style>
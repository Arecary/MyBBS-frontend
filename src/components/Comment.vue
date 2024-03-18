<template>
  <!--  评论开始  -->
  <div class="card">
    <h2 style="margin-bottom: 20px">Comments {{ commentCount }}</h2>

    <div style="margin-bottom: 20px">
      <el-input type="textarea" placeholder="Please enter a comment" v-model="commentContent"></el-input>
      <div style="text-align: right; margin-top: 5px">
        <el-button type="primary" @click="addComment">Post a comment</el-button>
      </div>
    </div>

    <div>
      <div style="display: flex; grid-gap: 20px; margin-bottom: 20px" v-for="item in commentList" :key="item.id">
        <img :src="item.avatar" alt="" style="width: 50px; height: 50px; border-radius: 50%">
        <div style="flex: 1">
          <!--                这是第一级评论-->
          <div style="margin-bottom: 10px">
            <div style="color: #666; margin-bottom: 10px">{{ item.userName }}</div>
            <div style="color: #444; margin-bottom: 10px">{{ item.content }}</div>
            <div style="color: #888; font-size: 13px; margin-bottom: 10px"><span style="margin-right: 20px">{{ item.time }}</span>
              <span style="cursor: pointer;" :class="{ 'comment-active' : item.showReplyInput }" @click="handleShowReplyInput(item)"><i class="el-icon-s-comment"></i>Comment</span>
              <span style="margin-left: 20px; cursor: pointer" @click="del(item.id)" v-if="item.userId === user.id"><i class="el-icon-delete"></i>Delete</span>
            </div>
            <div v-if="item.showReplyInput">
              <el-input type="textarea" placeholder="Please enter a comment" v-model="item.replyContent"></el-input>
              <div style="text-align: right; margin-top: 5px">
                <el-button type="primary" @click="addReplay(item)">Reply</el-button>
              </div>
            </div>
          </div>
          <!--                这是回复-->
          <div style="display: flex;  grid-gap: 20px; margin-bottom: 20px" v-for="sub in item.children" :key="sub.id">
            <img :src="sub.avatar" alt="" style="width: 50px; height: 50px; border-radius: 50%">
            <div style="flex: 1">
              <div style="color: #666; margin-bottom: 10px">{{ sub.userName }} <span style="color: #333" v-if="sub.replyUser !== sub.userName">
                <span  style="color: #888">
                  Replay to
                </span>
                  {{ sub.replyUser }}
              </span></div>
              <div style="color: #444; margin-bottom: 10px">{{ sub.content }}</div>
              <div style="color: #888; font-size: 13px; margin-bottom: 10px"><span style="margin-right: 20px">{{ sub.time }}</span>
                <span style="cursor: pointer;" :class="{ 'comment-active' : sub.showReplyInput }" @click="handleShowReplyInput(sub)"><i class="el-icon-s-comment"></i>Comment</span>
                <span style="margin-left: 20px; cursor: pointer" @click="del(sub.id)" v-if="sub.userId === user.id"><i class="el-icon-delete"></i>Delete</span>
              </div>
              <div v-if="sub.showReplyInput">
                <el-input type="textarea" placeholder="Please enter a comment" v-model="sub.replyContent"></el-input>
                <div style="text-align: right; margin-top: 5px">
                  <el-button type="primary" @click="addReplay(sub)">Reply</el-button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
  <!--  评论结束  -->

</template>

<script>
export default {
  name: "CommentComponent",
  props: {
    fid: null,
    module: null
  },
  data() {
    return {
      commentCount: 0,
      commentContent: '',
      commentList: [],
      user: JSON.parse(localStorage.getItem('xm-user') || '{}')
    }
  },
  created() {
    this.loadComment()
  },
  methods: {
    del(id) {   // 单个删除
      this.$confirm('Sure to delete?', 'delete', {type: "warning"}).then(response => {
        this.$request.delete('/comment/delete/' + id).then(res => {
          if (res.code === '200') {   // 表示操作成功
            this.$message.success('delete success')
            this.loadComment()
          } else {
            this.$message.error(res.msg)  // 弹出错误的信息
          }
        })
      }).catch(() => {
      })
    },
    handleShowReplyInput(item) {
      this.$set(item, 'showReplyInput', !item.showReplyInput)
    },
    addReplay(item) {
      this.$request.post('/comment/add', { pid: item.id, rootId: item.rootId, content: item.replyContent, fid: this.fid, module: this.module }).then(res => {
        if (res.code === '200') {
          this.$message.success('success')
          item.replyContent = ''
          this.loadComment()  // 重新加载数据
        }
      })
    },
    loadComment() {
      this.$request.get('/comment/selectForUser', {
        params: {  fid: this.fid, module: this.module }
      }).then(res => {
        this.commentList = res.data || []
      })

      this.$request.get('/comment/selectCount', {
        params: { fid: this.fid, module: this.module }
      }).then(res => {
        this.commentCount = res.data || 0
      })
    },
    addComment() {
      this.$request.post('/comment/add', { content: this.commentContent, fid: this.fid, module: this.module }).then(res => {
        if (res.code === '200') {
          this.$message.success('success')
          this.commentContent = ''
          this.loadComment()  // 重新加载数据
        }
      })
    },
  }
}
</script>

<style scoped>

</style>
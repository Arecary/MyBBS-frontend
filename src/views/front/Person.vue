<template>
  <div class="main-content" style="width: 50%">

    <el-tabs v-model="activeName" @tab-click="clickTab">
      <el-tab-pane label="Personal Info" name="个人资料">
        <person-page @update:user="updateUser"/>
      </el-tab-pane>
      <el-tab-pane label="My Blogs" name="我发表的博客">
        <!-- 只有当isUserRole为true时，即用户角色为USER时，才显示这个按钮 -->
        <div class="card" style="padding: 5px" v-if="isUserRole">
          <el-button type="primary" @click="addBlog">Post a new blog</el-button>
        </div>
        <div style="margin-top: 10px">
          <blog-list type="user" :show-opt="true" />
        </div>
      </el-tab-pane>
      <el-tab-pane label="Likes" name="我的点赞">
        <div style="margin-top: 10px">
          <BlogList type="like"/>
        </div>
      </el-tab-pane>
      <el-tab-pane label="Collect" name="我的收藏">
        <div style="margin-top: 10px">
          <BlogList type="collect"/>
        </div>
      </el-tab-pane>
      <el-tab-pane label="My Comment" name="我的评论">
        <div style="margin-top: 10px">
          <BlogList type="comment"/>
        </div>
      </el-tab-pane>
    </el-tabs>

    <Footer />
  </div>
</template>

<script>
import Footer from "@/components/Footer";
import PersonPage from "@/components/PersonPage";
import BlogList from "@/components/BlogList";

export default {
  components: {
    BlogList,
    Footer,
    PersonPage
  },
  data() {
    const validatePassword = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('Please confirm the password'))
      } else if (value !== this.user.newPassword) {
        callback(new Error('Confirm password is wrong'))
      } else {
        callback()
      }
    }
    return {
      user: JSON.parse(localStorage.getItem('xm-user') || '{}'),
      dialogVisible: false,

      rules: {
        password: [
          { required: true, message: 'Please enter old password', trigger: 'blur' },
        ],
        newPassword: [
          { required: true, message: 'Please enter new password', trigger: 'blur' },
        ],
        confirmPassword: [
          { validator: validatePassword, required: true, trigger: 'blur' },
        ],
      },
      activeName: '个人资料'
    }
  },
  created() {

  },
  computed: {
    // 添加一个计算属性来判断当前用户是否为USER角色
    isUserRole() {
      const user = JSON.parse(localStorage.getItem('xm-user') || '{}');
      return user.role === 'USER';
    }
  },
  methods: {
    updateUser(){
      // 触发父级的数据更新
      this.$emit('update:user')
    },
    addBlog() {
      window.open('/front/newBlog')
    },
    clickTab(tab) {
      console.log(tab)
    },
    update() {
      // 保存当前的用户信息到数据库
      this.$request.put('/user/update', this.user).then(res => {
        if (res.code === '200') {
          // 成功更新
          this.$message.success('save success')
          // 更新浏览器缓存里的用户信息
          localStorage.setItem('xm-user', JSON.stringify(this.user))

          // 触发父级的数据更新
          this.$emit('update:user')
        } else {
          this.$message.error(res.msg)
        }
      })
    },
    handleAvatarSuccess(response, file, fileList) {
      // 把user的头像属性换成上传的图片的链接
      this.$set(this.user, 'avatar', response.data)
    },
    // 修改密码
    updatePassword() {
      this.dialogVisible = true
    },
    save() {
      this.$refs.formRef.validate((valid) => {
        if (valid) {
          this.$request.put('/updatePassword', this.user).then(res => {
            if (res.code === '200') {
              // 成功更新
              this.$message.success('password change success')
              this.$router.push('/login')
            } else {
              this.$message.error(res.msg)
            }
          })
        }
      })
    }
  }
}
</script>

<style scoped>
/deep/.el-form-item__label {
  font-weight: bold;
}
/deep/.el-upload {
  border-radius: 50%;
}
/deep/.avatar-uploader .el-upload {
  border: 1px dashed #d9d9d9;
  cursor: pointer;
  position: relative;
  overflow: hidden;
  border-radius: 50%;
}
/deep/.avatar-uploader .el-upload:hover {
  border-color: #409EFF;
}
.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 120px;
  height: 120px;
  line-height: 120px;
  text-align: center;
  border-radius: 50%;
}
.avatar {
  width: 120px;
  height: 120px;
  display: block;
  border-radius: 50%;
}
</style>
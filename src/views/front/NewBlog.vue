<template>
  <div style="width: 50%; margin: 5px auto">

    <div class="card" style="margin-bottom: 100px">
      <div style="font-weight: bold; font-size: 24px; margin-bottom: 50px">Post/Edit a new blog</div>
      <el-form :model="form" label-width="100px" style="padding-right: 50px" :rules="rules" ref="formRef">
        <el-form-item label="Title" prop="title">
          <el-input v-model="form.title" placeholder="Title"></el-input>
        </el-form-item>
        <el-form-item label="Description" prop="descr">
          <el-input type="textarea" v-model="form.descr" placeholder="Description"></el-input>
        </el-form-item>
        <el-form-item label="Cover" prop="cover">
          <el-upload
              :action="$baseUrl + '/files/upload'"
              :headers="{ token: user.token }"
              list-type="picture"
              :on-success="handleCoverSuccess"
          >
            <el-button type="primary">upload cover</el-button>
          </el-upload>
        </el-form-item>
        <el-form-item label="Category" prop="categoryId">
          <el-select v-model="form.categoryId" style="width: 100%">
            <el-option v-for="item in categoryList" :key="item.id" :value="item.id" :label="item.name"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="Tags" prop="tags">
          <el-select v-model="tagsArr" multiple filterable allow-create default-first-option style="width: 100%">
            <el-option value="Warhammer"></el-option>
            <el-option value="Gundam"></el-option>
            <el-option value="Tank"></el-option>
            <el-option value="Ship"></el-option>
            <el-option value="Aircraft"></el-option>
            <el-option value="Spaceship"></el-option>
            <el-option value="Transformers"></el-option>
            <el-option value="Tokusatsu"></el-option>
            <el-option value="Others"></el-option>
            <el-option value="Unboxing New Products"></el-option>
            <el-option value="Assembly Process"></el-option>
            <el-option value="Exciting Reviews"></el-option>
            <el-option value="Second-hand Trading"></el-option>
            <el-option value="Casual Chat"></el-option>
            <el-option value="Rankings"></el-option>
            <el-option value="Handicraft Tutorial"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="Content" prop="content">
          <div id="editor"></div>
        </el-form-item>
      </el-form>
      <div style="text-align: center">
        <el-button type="primary" size="medium" style="width: 100px" @click="save">Post</el-button>
      </div>
    </div>

  </div>
</template>

<script>
import E from "wangeditor";
import hljs from "highlight.js";

export default {
  name: "NewBlog",

  data() {
    return {
      form: {},
      user: JSON.parse(localStorage.getItem('xm-user') || '{}'),
      rules: {},
      tagsArr: [],
      categoryList: [],
      editor: null,
      blogId: this.$route.query.blogId
    }
  },

  mounted() {
    this.$request.get('/category/selectAll').then(res =>{
      this.categoryList = res.data || []
    })


    this.$request.get('/blog/selectById/' + this.blogId).then(res =>{
      this.form = res.data || {} // 使用空对象作为备用值
      if (this.form.id) {
        this.tagsArr = JSON.parse(this.form.tags || '[]')
        setTimeout( ()=> {
          this.editor.txt.html(this.form.content)
        }, 0) // 异步 等页面渲染完再赋值
      }
    })

    this.setRichText()
  },

  methods: {
    save() {   // 保存按钮触发的逻辑  它会触发新增或者更新
      this.$refs.formRef.validate((valid) => {
        if (valid) {
          this.form.tags = JSON.stringify(this.tagsArr) //把json数组转换成json字符串存到数据库
          this.form.content = this.editor.txt.html()
          this.$request({
                          url: this.form.id ? '/blog/update' : '/blog/add',
                          method: this.form.id ? 'PUT' : 'POST',
                          data: this.form
                        }).then(res => {
            if (res.code === '200') {  // 表示成功保存
              this.$message.success('post success')
            } else {
              this.$message.error(res.msg)  // 弹出错误的信息
            }
          })
        }
      })
    },

    handleCoverSuccess(res){
      this.form.cover = res.data
    },
    setRichText() {
      this.$nextTick(() => {
        this.editor = new E(`#editor`)
        this.editor.highlight = hljs
        this.editor.config.uploadImgServer = this.$baseUrl + '/files/editor/upload'
        this.editor.config.uploadFileName = 'file'
        this.editor.config.uploadImgHeaders = {
          token: this.user.token
        }

        this.editor.config.uploadImgParams = {
          type: 'img',
        }
        this.editor.config.zIndex = 0
        this.editor.create()  // 创建
      })
    },

  }
}
</script>


<style scoped>

</style>
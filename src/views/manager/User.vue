<template>
  <div>
    <div class="search">
      <el-input placeholder="Please enter username" style="width: 200px; margin-right: 10px" v-model="username"></el-input>
      <el-input placeholder="Please enter name" style="width: 200px" v-model="name"></el-input>
      <el-button type="info" plain style="margin-left: 10px" @click="load(1)">search</el-button>
      <el-button type="warning" plain style="margin-left: 10px" @click="reset">reset</el-button>
    </div>

    <div class="operation">
      <el-button type="primary" plain @click="handleAdd">add</el-button>
      <el-button type="danger" plain @click="delBatch">batch delete</el-button>
    </div>

    <div class="table">
      <el-table :data="tableData" strip @selection-change="handleSelectionChange">
        <el-table-column type="selection" width="55" align="center"></el-table-column>
        <el-table-column prop="id" label="Id" width="70" align="center"
                         sortable></el-table-column>
        <el-table-column prop="username" label="Username"></el-table-column>
        <el-table-column prop="name" label="Name"></el-table-column>
        <el-table-column prop="phone" label="Phone"></el-table-column>
        <el-table-column prop="email" label="Email"></el-table-column>
        <el-table-column label="Avatar">
          <template v-slot="scope">
            <div style="display: flex; align-items: center">
              <el-image style="width: 40px; height: 40px; border-radius: 50%"
                        v-if="scope.row.avatar"
                        :src="scope.row.avatar" :preview-src-list="[scope.row.avatar]"></el-image>
            </div>
          </template>
        </el-table-column>
        <el-table-column prop="role" label="Role"></el-table-column>
        <el-table-column prop="sex" label="Gender"></el-table-column>
        <el-table-column prop="info" label="Profile" show-overflow-tooltip></el-table-column>
        <el-table-column prop="birth" label="Birth"></el-table-column>
        <el-table-column label="Action" align="center" width="180">
          <template v-slot="scope">
            <el-button size="mini" type="primary" plain @click="handleEdit(scope.row)">Edit
            </el-button>
            <el-button size="mini" type="danger" plain @click="del(scope.row.id)">Delete</el-button>
          </template>
        </el-table-column>
      </el-table>

      <div class="pagination">
        <el-pagination
            background
            @current-change="handleCurrentChange"
            :current-page="pageNum"
            :page-sizes="[5, 10, 20]"
            :page-size="pageSize"
            layout="total, prev, pager, next"
            :total="total">
        </el-pagination>
      </div>
    </div>


    <el-dialog title="User info" :visible.sync="fromVisible" width="40%"
               :close-on-click-modal="false" destroy-on-close>
      <el-form :model="form" label-width="100px" style="padding-right: 50px" :rules="rules"
               ref="formRef">
        <el-form-item label="Username" prop="username">
          <el-input v-model="form.username" placeholder="username"></el-input>
        </el-form-item>
        <el-form-item label="Name" prop="name">
          <el-input v-model="form.name" placeholder="name"></el-input>
        </el-form-item>
        <el-form-item label="Phone" prop="phone">
          <el-input v-model="form.phone" placeholder="phone"></el-input>
        </el-form-item>
        <el-form-item label="Email" prop="email">
          <el-input v-model="form.email" placeholder="email"></el-input>
        </el-form-item>
        <el-form-item label="Avatar">
          <el-upload
              class="avatar-uploader"
              :action="$baseUrl + '/files/upload'"
              :headers="{ token: user.token }"
              list-type="picture"
              :on-success="handleAvatarSuccess"
          >
            <el-button type="primary">upload avatar</el-button>
          </el-upload>
        </el-form-item>
        <el-form-item label="Gender" prop="sex">
          <el-radio-group v-model="form.sex">
            <el-radio label="Male"></el-radio>
            <el-radio label="Female"></el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="Personal profile" prop="info">
          <el-input type="textarea" v-model="form.info" placeholder="Personal profile"></el-input>
        </el-form-item>
        <el-form-item label="Birth" prop="brith">
          <el-date-picker value-format="yyyy-MM-dd" format="yyyy-MM-dd" v-model="form.birth"
                          style="width: 100%"></el-date-picker>
        </el-form-item>
      </el-form>

      <div slot="footer" class="dialog-footer">
        <el-button @click="fromVisible = false">cancel</el-button>
        <el-button type="primary" @click="save">confirm</el-button>
      </div>
    </el-dialog>


  </div>
</template>

<script>
export default {
  name: "User",
  data() {
    return {
      tableData: [],  // 所有的数据
      pageNum: 1,   // 当前的页码
      pageSize: 10,  // 每页显示的个数
      total: 0,
      username: null,
      name: null,
      fromVisible: false,
      form: {},
      user: JSON.parse(localStorage.getItem('xm-user') || '{}'),
      rules: {
        username: [
          {required: true, message: 'Please enter username', trigger: 'blur'},
        ]
      },
      ids: []
    }
  },
  created() {
    this.load(1)
  },
  methods: {
    handleAdd() {   // 新增数据
      this.form = {}  // 新增数据的时候清空数据
      this.fromVisible = true   // 打开弹窗
    },
    handleEdit(row) {   // 编辑数据
      this.form = JSON.parse(JSON.stringify(row))  // 给form对象赋值  注意要深拷贝数据
      this.fromVisible = true   // 打开弹窗
    },
    save() {   // 保存按钮触发的逻辑  它会触发新增或者更新
      this.$refs.formRef.validate((valid) => {
        if (valid) {
          this.$request({
                          url: this.form.id ? '/user/update' : '/user/add',
                          method: this.form.id ? 'PUT' : 'POST',
                          data: this.form
                        }).then(res => {
            if (res.code === '200') {  // 表示成功保存
              this.$message.success('save success')
              this.load(1)
              this.fromVisible = false
            } else {
              this.$message.error(res.msg)  // 弹出错误的信息
            }
          })
        }
      })
    },
    del(id) {   // 单个删除
      this.$confirm('Sure to delete？', 'delete', {type: "warning"}).then(response => {
        this.$request.delete('/user/delete/' + id).then(res => {
          if (res.code === '200') {   // 表示操作成功
            this.$message.success('delete success')
            this.load(1)
          } else {
            this.$message.error(res.msg)  // 弹出错误的信息
          }
        })
      }).catch(() => {
      })
    },
    handleSelectionChange(rows) {   // 当前选中的所有的行数据
      this.ids = rows.map(v => v.id)
    },
    delBatch() {   // 批量删除
      if (!this.ids.length) {
        this.$message.warning('Please select at least one data')
        return
      }
      this.$confirm('Are you sure you want to delete these data in batches?', 'delete', {type: "warning"}).then(response => {
        this.$request.delete('/user/delete/batch', {data: this.ids}).then(res => {
          if (res.code === '200') {   // 表示操作成功
            this.$message.success('delete success')
            this.load(1)
          } else {
            this.$message.error(res.msg)  // 弹出错误的信息
          }
        })
      }).catch(() => {
      })
    },
    load(pageNum) {  // 分页查询
      if (pageNum) {
        this.pageNum = pageNum
      }
      this.$request.get('/user/selectPage', {
        params: {
          pageNum: this.pageNum,
          pageSize: this.pageSize,
          username: this.username,
          name: this.name,
        }
      }).then(res => {
        this.tableData = res.data?.list
        this.total = res.data?.total
      })
    },
    reset() {
      this.username = null
      this.name = null
      this.load(1)
    },
    handleCurrentChange(pageNum) {
      this.load(pageNum)
    },
    handleAvatarSuccess(response, file, fileList) {
      // 把头像属性换成上传的图片的链接
      this.form.avatar = response.data
    },
  }
}
</script>

<style scoped>

</style>
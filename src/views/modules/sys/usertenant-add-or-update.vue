<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="租户名称" prop="tenantName">
      <el-input v-model="dataForm.tenantName" placeholder="租户名称"></el-input>
    </el-form-item>
    <el-form-item label="租户详情" prop="tenantDesc">
      <el-input v-model="dataForm.tenantDesc" placeholder="租户详情"></el-input>
    </el-form-item>
    <el-form-item label="状态" size="mini" prop="status">
      <el-radio-group v-model="dataForm.status">
        <el-radio :label="0">禁用</el-radio>
        <el-radio :label="1">正常</el-radio>
      </el-radio-group>
    </el-form-item>
    <el-form-item label="管理员电话" prop="managerPhone">
      <el-input v-model="dataForm.managerPhone" placeholder="管理员电话"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          tenantName: '',
          tenantDesc: '',
          status: '',
          managerPhone: '',
          createTime: '',
          updateTime: ''
        },
        dataRule: {
          tenantName: [
            { required: true, message: '租户名称不能为空', trigger: 'blur' }
          ],
          tenantDesc: [
            { required: true, message: '租户详情不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '状态 0:启用 1:未启用不能为空', trigger: 'blur' }
          ],
          managerPhone: [
            { required: true, message: '管理员电话不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ],
          updateTime: [
            { required: true, message: '修改时间不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/sys/usertenant/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.tenantName = data.usertenant.tenantName
                this.dataForm.tenantDesc = data.usertenant.tenantDesc
                this.dataForm.status = data.usertenant.status
                this.dataForm.managerPhone = data.usertenant.managerPhone
                this.dataForm.createTime = data.usertenant.createTime
                this.dataForm.updateTime = data.usertenant.updateTime
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/sys/usertenant/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'tenantName': this.dataForm.tenantName,
                'tenantDesc': this.dataForm.tenantDesc,
                'status': this.dataForm.status,
                'managerPhone': this.dataForm.managerPhone,
                'createTime': this.dataForm.createTime,
                'updateTime': this.dataForm.updateTime
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>

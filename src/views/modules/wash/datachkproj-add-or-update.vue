<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="检查项目代码" prop="chkProjCd">
      <el-input v-model="dataForm.chkProjCd" placeholder="检查项目代码"></el-input>
    </el-form-item>
    <el-form-item label="检查项目名称" prop="chkProjNm">
      <el-input v-model="dataForm.chkProjNm" placeholder="检查项目名称"></el-input>
    </el-form-item>
    <el-form-item label="检查项目描述" prop="chkProjDesc">
      <el-input v-model="dataForm.chkProjDesc" placeholder="检查项目描述"></el-input>
    </el-form-item>
    <el-form-item label="检查项目状态" prop="chkProjStus">
      <el-input v-model="dataForm.chkProjStus" placeholder="检查项目状态"></el-input>
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
          chkProjid: 0,
          chkProjCd: '',
          chkProjNm: '',
          chkProjDesc: '',
          chkProjStus: ''
        },
        dataRule: {
          chkProjCd: [
            { required: true, message: '检查项目代码不能为空', trigger: 'blur' }
          ],
          chkProjNm: [
            { required: true, message: '检查项目名称不能为空', trigger: 'blur' }
          ],
          chkProjDesc: [
            { required: true, message: '检查项目描述不能为空', trigger: 'blur' }
          ],
          chkProjStus: [
            { required: true, message: '检查项目状态不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.chkProjid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.chkProjid) {
            this.$http({
              url: this.$http.adornUrl(`/wash/datachkproj/info/${this.dataForm.chkProjid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.chkProjCd = data.datachkproj.chkProjCd
                this.dataForm.chkProjNm = data.datachkproj.chkProjNm
                this.dataForm.chkProjDesc = data.datachkproj.chkProjDesc
                this.dataForm.chkProjStus = data.datachkproj.chkProjStus
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
              url: this.$http.adornUrl(`/wash/datachkproj/${!this.dataForm.chkProjid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'chkProjid': this.dataForm.chkProjid || undefined,
                'chkProjCd': this.dataForm.chkProjCd,
                'chkProjNm': this.dataForm.chkProjNm,
                'chkProjDesc': this.dataForm.chkProjDesc,
                'chkProjStus': this.dataForm.chkProjStus
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

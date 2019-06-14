<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="数据探查模板ID" prop="dataExplTmplid">
      <el-input v-model="dataForm.dataExplTmplid" placeholder="数据探查模板ID"></el-input>
    </el-form-item>
    <el-form-item label="项目类型" prop="projType">
      <el-input v-model="dataForm.projType" placeholder="项目类型"></el-input>
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
          dataTblid: 0,
          dataExplTmplid: '',
          projType: ''
        },
        dataRule: {
          dataExplTmplid: [
            { required: true, message: '数据探查模板ID不能为空', trigger: 'blur' }
          ],
          projType: [
            { required: true, message: '项目类型不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.dataTblid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.dataTblid) {
            this.$http({
              url: this.$http.adornUrl(`/wash/datatblexplproj/info/${this.dataForm.dataTblid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.dataExplTmplid = data.datatblexplproj.dataExplTmplid
                this.dataForm.projType = data.datatblexplproj.projType
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
              url: this.$http.adornUrl(`/wash/datatblexplproj/${!this.dataForm.dataTblid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'dataTblid': this.dataForm.dataTblid || undefined,
                'dataExplTmplid': this.dataForm.dataExplTmplid,
                'projType': this.dataForm.projType
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

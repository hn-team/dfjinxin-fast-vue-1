<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="数据清洗运算ID" prop="dataWashCmpuid">
      <el-input v-model="dataForm.dataWashCmpuid" placeholder="数据清洗运算ID"></el-input>
    </el-form-item>
    <el-form-item label="检查项目ID" prop="chkProjid">
      <el-input v-model="dataForm.chkProjid" placeholder="检查项目ID"></el-input>
    </el-form-item>
    <el-form-item label="执行顺序" prop="exctOrd">
      <el-input v-model="dataForm.exctOrd" placeholder="执行顺序"></el-input>
    </el-form-item>
    <el-form-item label="参数ID" prop="paraid">
      <el-input v-model="dataForm.paraid" placeholder="参数ID"></el-input>
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
          dataWashCmpuid: '',
          chkProjid: '',
          exctOrd: '',
          paraid: ''
        },
        dataRule: {
          dataWashCmpuid: [
            { required: true, message: '数据清洗运算ID不能为空', trigger: 'blur' }
          ],
          chkProjid: [
            { required: true, message: '检查项目ID不能为空', trigger: 'blur' }
          ],
          exctOrd: [
            { required: true, message: '执行顺序不能为空', trigger: 'blur' }
          ],
          paraid: [
            { required: true, message: '参数ID不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/wash/datatblwashproj/info/${this.dataForm.dataTblid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.dataWashCmpuid = data.datatblwashproj.dataWashCmpuid
                this.dataForm.chkProjid = data.datatblwashproj.chkProjid
                this.dataForm.exctOrd = data.datatblwashproj.exctOrd
                this.dataForm.paraid = data.datatblwashproj.paraid
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
              url: this.$http.adornUrl(`/wash/datatblwashproj/${!this.dataForm.dataTblid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'dataTblid': this.dataForm.dataTblid || undefined,
                'dataWashCmpuid': this.dataForm.dataWashCmpuid,
                'chkProjid': this.dataForm.chkProjid,
                'exctOrd': this.dataForm.exctOrd,
                'paraid': this.dataForm.paraid
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

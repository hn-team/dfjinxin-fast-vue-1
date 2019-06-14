<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="数据表ID" prop="dataTblid">
      <el-input v-model="dataForm.dataTblid" placeholder="数据表ID"></el-input>
    </el-form-item>
    <el-form-item label="字段物理名称" prop="fldPhysNm">
      <el-input v-model="dataForm.fldPhysNm" placeholder="字段物理名称"></el-input>
    </el-form-item>
    <el-form-item label="字段中文名称" prop="fldCnNm">
      <el-input v-model="dataForm.fldCnNm" placeholder="字段中文名称"></el-input>
    </el-form-item>
    <el-form-item label="字段数据类型" prop="fldDataType">
      <el-input v-model="dataForm.fldDataType" placeholder="字段数据类型"></el-input>
    </el-form-item>
    <el-form-item label="字段描述" prop="fldDesc">
      <el-input v-model="dataForm.fldDesc" placeholder="字段描述"></el-input>
    </el-form-item>
    <el-form-item label="字段顺序" prop="fldOrd">
      <el-input v-model="dataForm.fldOrd" placeholder="字段顺序"></el-input>
    </el-form-item>
    <el-form-item label="是否主键" prop="ifPk">
      <el-input v-model="dataForm.ifPk" placeholder="是否主键"></el-input>
    </el-form-item>
    <el-form-item label="是否可以为空" prop="ifCanNull">
      <el-input v-model="dataForm.ifCanNull" placeholder="是否可以为空"></el-input>
    </el-form-item>
    <el-form-item label="创建日期" prop="createDt">
      <el-input v-model="dataForm.createDt" placeholder="创建日期"></el-input>
    </el-form-item>
    <el-form-item label="更新日期" prop="updDt">
      <el-input v-model="dataForm.updDt" placeholder="更新日期"></el-input>
    </el-form-item>
    <el-form-item label="删除日期" prop="delDt">
      <el-input v-model="dataForm.delDt" placeholder="删除日期"></el-input>
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
          fldid: 0,
          dataTblid: '',
          fldPhysNm: '',
          fldCnNm: '',
          fldDataType: '',
          fldDesc: '',
          fldOrd: '',
          ifPk: '',
          ifCanNull: '',
          createDt: '',
          updDt: '',
          delDt: ''
        },
        dataRule: {
          dataTblid: [
            { required: true, message: '数据表ID不能为空', trigger: 'blur' }
          ],
          fldPhysNm: [
            { required: true, message: '字段物理名称不能为空', trigger: 'blur' }
          ],
          fldCnNm: [
            { required: true, message: '字段中文名称不能为空', trigger: 'blur' }
          ],
          fldDataType: [
            { required: true, message: '字段数据类型不能为空', trigger: 'blur' }
          ],
          fldDesc: [
            { required: true, message: '字段描述不能为空', trigger: 'blur' }
          ],
          fldOrd: [
            { required: true, message: '字段顺序不能为空', trigger: 'blur' }
          ],
          ifPk: [
            { required: true, message: '是否主键不能为空', trigger: 'blur' }
          ],
          ifCanNull: [
            { required: true, message: '是否可以为空不能为空', trigger: 'blur' }
          ],
          createDt: [
            { required: true, message: '创建日期不能为空', trigger: 'blur' }
          ],
          updDt: [
            { required: true, message: '更新日期不能为空', trigger: 'blur' }
          ],
          delDt: [
            { required: true, message: '删除日期不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.fldid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.fldid) {
            this.$http({
              url: this.$http.adornUrl(`/meta/datafld/info/${this.dataForm.fldid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.dataTblid = data.datafld.dataTblid
                this.dataForm.fldPhysNm = data.datafld.fldPhysNm
                this.dataForm.fldCnNm = data.datafld.fldCnNm
                this.dataForm.fldDataType = data.datafld.fldDataType
                this.dataForm.fldDesc = data.datafld.fldDesc
                this.dataForm.fldOrd = data.datafld.fldOrd
                this.dataForm.ifPk = data.datafld.ifPk
                this.dataForm.ifCanNull = data.datafld.ifCanNull
                this.dataForm.createDt = data.datafld.createDt
                this.dataForm.updDt = data.datafld.updDt
                this.dataForm.delDt = data.datafld.delDt
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
              url: this.$http.adornUrl(`/meta/datafld/${!this.dataForm.fldid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'fldid': this.dataForm.fldid || undefined,
                'dataTblid': this.dataForm.dataTblid,
                'fldPhysNm': this.dataForm.fldPhysNm,
                'fldCnNm': this.dataForm.fldCnNm,
                'fldDataType': this.dataForm.fldDataType,
                'fldDesc': this.dataForm.fldDesc,
                'fldOrd': this.dataForm.fldOrd,
                'ifPk': this.dataForm.ifPk,
                'ifCanNull': this.dataForm.ifCanNull,
                'createDt': this.dataForm.createDt,
                'updDt': this.dataForm.updDt,
                'delDt': this.dataForm.delDt
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

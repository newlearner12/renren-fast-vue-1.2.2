<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="资源类型" prop="resourceType">
      <el-input v-model="dataForm.resourceType" placeholder="资源类型"></el-input>
    </el-form-item>
    <el-form-item label="资源名" prop="name">
      <el-input v-model="dataForm.name" placeholder="资源名"></el-input>
    </el-form-item>
    <el-form-item label="民族" prop="nation">
      <el-input v-model="dataForm.nation" placeholder="民族"></el-input>
    </el-form-item>
    <el-form-item label="描述" prop="description">
      <el-input v-model="dataForm.description" placeholder="描述"></el-input>
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
          resourceId: 0,
          resourceType: '',
          name: '',
          nation: '',
          description: ''
        },
        dataRule: {
          resourceType: [
            { required: true, message: '资源类型不能为空', trigger: 'blur' }
          ],
          name: [
            { required: true, message: '资源名不能为空', trigger: 'blur' }
          ],
          nation: [
            { required: true, message: '民族不能为空', trigger: 'blur' }
          ],
          description: [
            { required: true, message: '描述不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.resourceId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.resourceId) {
            this.$http({
              url: this.$http.adornUrl(`/generator/folkresource/info/${this.dataForm.resourceId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.resourceType = data.folkResource.resourceType
                this.dataForm.name = data.folkResource.name
                this.dataForm.nation = data.folkResource.nation
                this.dataForm.description = data.folkResource.description
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
              url: this.$http.adornUrl(`/generator/folkresource/${!this.dataForm.resourceId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'resourceId': this.dataForm.resourceId || undefined,
                'resourceType': this.dataForm.resourceType,
                'name': this.dataForm.name,
                'nation': this.dataForm.nation,
                'description': this.dataForm.description
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

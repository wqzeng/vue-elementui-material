<template>
    <el-dialog :title="title" :visible.sync="dialogVisible" width="30%" :before-close="hideDialog">
        <div class="add">
            <el-form ref="form" :model="formData" :rules="rules" label-width="80px">
                <el-form-item label="名称" prop="name">
                    <el-input v-model="formData.name" maxlength="10" show-word-limit></el-input>
                </el-form-item>

                <el-form-item label="编码" prop="code">
                    <el-input v-model="formData.code" maxlength="10" show-word-limit placeholder="输入名称首字母（大写）"></el-input>
                </el-form-item>
            </el-form>
            <div slot="footer" align="center">
                <el-button size="mini" @click="$emit('hideDialog')">取消</el-button>
                <el-button type="primary" size="mini" @click="submit('form')">确定</el-button>
            </div>
        </div>
    </el-dialog>
</template>

<script>
    export default {
        name: "addMetadata",
        props: {
            rowData: {
                type: Object,
                default: function () {
                    return {}
                }
            }
        },
        data() {
            return {
                dialogVisible: true,
                formData: {
                    id:'',
                    name: '',
                    code: ''
                },
                title:'新建编码',
                rules: {
                    name: [{required: true, message: "请输入名称", trigger: 'blur'}],
                    code: [{required: true, message: "请输入编码（如：名称首字母大写）", trigger: 'blur'}]
                }
            }
        },
        created() {
            this.formData=Object.assign({},this.formData,this.rowData)
            if(this.formData.id){
                this.title='修改编码'
            }
        },
        methods: {
            hideDialog() {
                this.$emit('hideDialog')
                this.$emit('query')
            },
            submit(addForm) {
                this.$refs[addForm].validate(valid => {
                    if (valid) {
                        if(this.formData.id){
                            this.$axios.post("/metadata/update", {
                                id: this.formData.id,
                                name: this.formData.name,
                                code: this.formData.code
                            }).then(res => {
                                if (res.data.code === 200) {
                                    this.$message.success("修改成功");
                                    this.hideDialog()
                                } else {
                                    this.$message.error(res.data.message);
                                }
                            }).catch(err => {
                                this.$message.error("修改失败！请稍后重试")
                            });
                        }else {
                            this.$axios.post("/metadata/add", {
                                name: this.formData.name,
                                code: this.formData.code
                            }).then(res => {
                                if (res.data.code === 200) {
                                    this.$message.success("新建成功");
                                    this.hideDialog()
                                } else {
                                    this.$message.error(res.data.message);
                                }
                            }).catch(err => {
                                this.$message.error("新建失败！请稍后重试")
                            });
                        }
                    }else {
                        return false;
                    }
                });

            }
        }
    }
</script>

<style scoped>
    .add {
        margin-right: 40px;
        /*margin-top: 30px;*/
        margin-left: 10px;
    }
</style>
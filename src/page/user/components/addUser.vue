<template>
    <el-dialog :title="title" :visible.sync="dialogVisible" width="30%" :before-close="hideDialog">
        <div class="add">
            <el-form ref="form" :model="formData" label-width="80px" :rules="rules">
                <el-form-item label="姓名">
                    <el-input v-model="formData.userName"></el-input>
                </el-form-item>
                <el-form-item label="登录账号">
                    <el-input v-model="formData.loginName" v-if="formData.id" disabled></el-input>
                    <el-input v-model="formData.loginName" v-else></el-input>
                </el-form-item>
                <el-form-item label="密码">
                    <el-input v-model="formData.password" type="password"></el-input>
                </el-form-item>
                <el-form-item label="确认密码">
                    <el-input v-model="formData.rePassword" type="password"></el-input>
                </el-form-item>
                <el-form-item label="电话">
                    <el-input v-model="formData.userMobile" placeholder="请输入数字"></el-input>
                </el-form-item>
                <el-form-item label="性别">
                    <el-select v-model="formData.userSex" placeholder="请选择性别">
                        <el-option label="男" value="1"></el-option>
                        <el-option label="女" value="2"></el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="门店">
                    <el-select v-model="formData.deptNo" placeholder="请选择门店">
                        <el-option label="采购部" value="1"></el-option>
                        <el-option label="库管部" value="2"></el-option>
                        <el-option label="物资部" value="3"></el-option>
                    </el-select>
                </el-form-item>
            </el-form>
        </div>
            <div slot="footer" align="center">
                <el-button size="mini" @click="$emit('hideDialog')">取消</el-button>
                <el-button type="primary" size="mini" @click="submit('form')">确定</el-button>
            </div>
    </el-dialog>
</template>

<script>
    export default {
        name: "addUser",
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
                    userName: '',
                    loginName: '',
                    password:'',
                    rePassword:'',
                    userMobile:'',
                    userSex:'',
                    deptNo:''
                },
                title:'添加人员',
                rules: {
                    userName: [{required: true, message: "请输入姓名", trigger: 'blur'}],
                    loginName: [{required: true, message: "请输入登录名（英文或数字）", trigger: 'blur'}]
                }
            }
        },
        created() {
            this.formData=Object.assign({},this.formData,this.rowData)
            if(this.formData.id){
                this.title='修改人员'
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
                            this.$axios.post("/user/update", {
                                id: this.formData.id,
                                loginName:this.formData.loginName,
                                userName: this.formData.userName,
                                password: this.formData.password,
                                userMobile:this.formData.userMobile,
                                userSex: this.formData.userSex,
                                deptNo: this.formData.deptNo
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
                            this.$axios.post("/user/add", {
                                loginName:this.formData.loginName,
                                userName: this.formData.userName,
                                password: this.formData.password,
                                userMobile:this.formData.userMobile,
                                userSex: this.formData.userSex,
                                deptNo: this.formData.deptNo
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
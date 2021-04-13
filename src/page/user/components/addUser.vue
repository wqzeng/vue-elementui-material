<template>
    <el-dialog :title="title" :visible.sync="dialogVisible" width="30%" :before-close="hideDialog">
        <div class="add">
            <el-form ref="form" :model="formData" label-width="80px" :rules="rules">
                <el-form-item label="姓名" prop="userName">
                    <el-input v-model="formData.userName" clearable></el-input>
                </el-form-item>
                <el-form-item label="登录账号" prop="loginName">
                    <el-input v-model="formData.loginName" v-if="formData.id" disabled></el-input>
                    <el-input v-model="formData.loginName" v-else placeholder="请输入字母或数字账号" maxlength="18" show-word-limit clearable></el-input>
                </el-form-item>
                <el-form-item label="密码" prop="password">
                    <el-input v-model="formData.password" type="password" clearable></el-input>
                </el-form-item>
                <el-form-item label="确认密码" prop="rePassword">
                    <el-input v-model="formData.rePassword" type="password" placeholder="确认2次密码一致" clearable></el-input>
                </el-form-item>
                <el-form-item label="电话" prop="userMobile">
                    <el-input v-model="formData.userMobile" placeholder="请输入11位手机号" maxlength="11" show-word-limit clearable></el-input>
                </el-form-item>
                <el-form-item label="性别" >
                    <el-select v-model="formData.userSex" placeholder="请选择性别">
                        <el-option v-for="(item,index) in sexEnum" :key="item.value" :value="item.value" :label="item.label"></el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="门店">
                    <el-select v-model="formData.deptNo" placeholder="请选择门店">
                        <el-option v-for="(value,key) in deptOption" :key="value" :value="key" :label="value"></el-option>
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
            },
            deptOption:{
                type: Object
            }
        },
        data() {
            const  checkPassword = (rule, value, callback) => {
                if (value === this.formData.password) {
                    return callback()
                }
                callback(new Error('2次密码不一致'))
            }
            return {
                dialogVisible: true,
                formData: {
                    id:'',
                    userName: '',
                    loginName: '',
                    password:'',
                    rePassword:'',
                    userMobile:'',
                    userSex:null,
                    deptNo:''
                },
                title:'添加人员',
                sexEnum:[{label:"男",value:1},{label:"女",value:2}],
                rules: {
                    userName: [{required: true, message: "请输入姓名", trigger: 'blur'}],
                    loginName: [{required: true, message: "请输入登录名（英文或数字）", trigger: 'blur'}],
                    userMobile:[{required:true,message:"请输入手机号",trigger:'blur'}],
                    password:[{required:true,message:"请输入密码",trigger:'blur'}],
                    rePassword:[{required:true,message:"请输入确认密码",trigger:'blur'},{validator:checkPassword,trigger:'blur'}]
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
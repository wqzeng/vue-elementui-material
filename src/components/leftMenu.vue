<template>
    <el-menu :default-active="$route.path"
             unique-opened
             style="min-height: 100%;" router>
        <el-menu-item index="/home/first">
            <i class="el-icon-odometer"></i>
            <span slot="title">首页</span>
        </el-menu-item>
        <el-submenu index="2">
            <template slot="title">
                <i class="el-icon-truck"></i>
                <span>进货管理</span>
            </template>
            <el-menu-item-group>
                <el-menu-item index="/home/needPlan">需求计划申请</el-menu-item>
            </el-menu-item-group>

            <el-menu-item-group>
                <el-menu-item index="/home/needPlanp">需求计划审批</el-menu-item>
            </el-menu-item-group>

            <el-menu-item-group>
                <el-menu-item index="/home/pruchase">请购</el-menu-item>
            </el-menu-item-group>

            <el-menu-item-group>
                <el-menu-item index="/home/No">请购通过</el-menu-item>
            </el-menu-item-group>
        </el-submenu>
        <el-submenu index="3">
            <template slot="title">
                <i class="el-icon-shopping-bag-1"></i>
                <span>销售管理</span>
            </template>
            <el-menu-item-group>
                <el-menu-item index="/home/No">物资接收</el-menu-item>
            </el-menu-item-group>
            <el-menu-item-group>
                <el-menu-item index="/home/No">物资出库</el-menu-item>
            </el-menu-item-group>
        </el-submenu>
        <el-submenu index="4">
            <template slot="title">
                <i class="el-icon-house"></i>
                <span>库存管理</span>
            </template>
            <el-menu-item-group>
                <el-menu-item index="/home/No">仓库维护</el-menu-item>
            </el-menu-item-group>

            <el-menu-item-group>
                <el-menu-item index="/home/No">查看库存</el-menu-item>
            </el-menu-item-group>
        </el-submenu>
        <el-submenu index="5">
            <template slot="title">
                <i class="el-icon-refresh"></i>
                <span>基础信息</span>
            </template>
            <el-menu-item-group>
                <el-menu-item index="/base/metadata">编码管理</el-menu-item>
            </el-menu-item-group>
        </el-submenu>
        <el-submenu index="6">
            <template slot="title">
                <i class="el-icon-aim"></i>
                <span>系统管理</span>
            </template>
            <el-menu-item-group>
                <el-menu-item index="/system/user">人员管理</el-menu-item>
            </el-menu-item-group>
        </el-submenu>
        <el-menu-item index="/home/callme">
            <i class="el-icon-phone"></i>
            <span slot="title">请联系我</span>
        </el-menu-item>

    </el-menu>
</template>

<script>
    export default {
        name: "leftMenu"
    }
</script>

<style scoped>

</style>
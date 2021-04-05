<template>
  <div class="home">
    <head-top></head-top>
    <el-container style="height:100%">
      <div style="border-right:1px solid #dcdfe6;" :style="height" class="leftm">
        <el-aside width="200px">
          <left-menu></left-menu>
        </el-aside>
      </div>

      <el-container>
        <!-- 主要内容 -->
        <el-main>
          <!-- <web-map></web-map> -->

          <div class="bar">
            <nav-bar></nav-bar>
          </div>
          <transition name="slide-fade">
            <router-view></router-view>
          </transition>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script>
import headTop from "./headTop.vue";
import leftMenu from "./leftMenu"
import charts from "../page/someCharts";
import materialInfo from "../page/materialInfo";
import navBar from "./navBar";
export default {
  data() {
    return {
      height: {
        height: ""
      },
      isCollapse: true
    };
  },
  components: {
    headTop,
    navBar,
    leftMenu
  },
  mounted() {},
  methods: {
    hh() {
      this.height.height = window.innerHeight - 60 + "px";
    },
    handleOpen(key, keyPath) {
      console.log(key, keyPath);
    },
    handleClose(key, keyPath) {
      console.log(key, keyPath);
    }
  },
  created() {
    this.hh();
  },
  computed: {},
  watch: {}
};
</script>

<style>
html,
body {
  margin: 0;
  padding: 0;
  font-family: sans-serif;
  background-color: #fff;
  height: 100%;
}

.el-aside {
  background-color: #545c64;
}
.el-menu {
  border: none;
}
.bar {
  margin-bottom: 8px;
}
.slide-fade {
  position: absolute;
  left: 0;
  right: 0;
}
.slide-fade-enter-active {
  transition: all 1.2s ease;
}
.slide-fade-leave-active {
  transition: all 0.1s cubic-bezier(2, 0.5, 0.8, 1);
}
.slide-fade-enter,
.slide-fade-leave-to {
  left: 0;
  right: 0;
  transform: translateX(50px);
  opacity: 0;
}
.el-menu-item-group__title {
  padding: 0px 0px 0px 20px;
}
</style>

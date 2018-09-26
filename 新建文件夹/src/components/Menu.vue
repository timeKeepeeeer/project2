<template>
  <div class="menu-box">
    <div class="fixed-menu">
      <img src="../../static/img/menu.png" alt="" @click="showMenu">
      <transition enter-active-class="animated fadeInRightBig" leave-active-class="animated fadeOutRightBig">
        <div v-show="menuShow" v-clickoutside="outsideMenu" >
          <div class="triangle"></div>
          <div class="menu-content">&nbsp;
            <!-- 返回首页 -->
            <div class="back-to-homepage">
              <img src="../../static/img/backToHomepage.png" alt="" style="vertical-align:middle;">
              <span class="back-to-homepage-title">首页</span>
            </div>
            <!-- 综合监控 -->
            <div class="menu-content-item">
              <div class="menu-content-item-left">
                <img src="../../static/img/iscs.png" alt="">&nbsp;
                <span class="menu-content-item-title">综合监控</span>
              </div>
              <div class="menu-content-item-right">
                <div>一次接线图</div>
                <div>风力监控</div>
                <div>光伏监控</div>
                <div>储能监控</div>
                <div>负荷监控</div>
                <div>箱变监控</div>
                <div>实时监控</div>
                <div>告警分析</div>
              </div>
            </div>
            <div class="menu-content-item">
              <div class="menu-content-item-left">
                <img src="../../static/img/working-management.png" alt="">&nbsp;
                <span class="menu-content-item-title">运行管理</span>
              </div>
              <div class="menu-content-item-right">
                <div>电源管理</div>
                <div>负荷管理</div>
                <div>能效分析</div>
                <div>运维管理</div>
              </div>
            </div>
            <div class="menu-content-item">
              <div class="menu-content-item-left">
                <img src="../../static/img/working-optimization.png" alt="">&nbsp;
                <span class="menu-content-item-title">运行优化</span>
              </div>
              <div class="menu-content-item-right">
                <div>光伏与发电预测</div>
                <div>风力发电预测</div>
                <div>负荷预测</div>
                <div>计划编制</div>
                <div>充电调度</div>
                <div>运维评价</div>
              </div>
            </div>
            <div class="menu-content-item">
              <div class="menu-content-item-left">
                <img src="../../static/img/analysis-resolution.png" alt="">&nbsp;
                <span class="menu-content-item-title">分析决策</span>
              </div>
              <div class="menu-content-item-right">
                <div>全息反演</div>
                <div>运维仿真</div>
                <div>需求侧响应</div>
                <div>辅助服务</div>
              </div>
            </div>
            <div class="menu-content-item">
              <div class="menu-content-item-left">
                <img src="../../static/img/system-management.png" alt="">&nbsp;
                <span class="menu-content-item-title">系统管理</span>
              </div>
              <div class="menu-content-item-right">
                <div>图模管理</div>
                <div>参数管理</div>
                <div>服务监视</div>
                <div>采集监视</div>
              </div>
            </div>
          </div>
        </div>
      </transition>
    </div>
    <div class="shadow" v-if="shadowShow"></div>
  </div>
</template>
<script>
import clickoutside from '../clickoutside';
export default {
  name: 'Menu',
  directives: {
    clickoutside
  },
  data () {
    return {
      menuShow: false,
      shadowShow: false
    };
  },
  methods: {
    showMenu () {
      this.menuShow = true;
      this.shadowShow = true;
    },
    outsideMenu () {
      if (this.menuShow) {
        this.menuShow = false;
        this.shadowShow = false;
      }
    }
  }
};
</script>
<style scoped>
@media screen and (min-width:878px) and (max-width:1920px) {
  .fixed-menu {
    position: absolute;
    top: 70px;
    right: 1.6%;
    z-index: 1004;
  }
  .fixed-menu>img:hover {
    cursor: pointer;
  }
  .fixed-menu>img {
    margin-left: 800px;
  }
  .triangle {
    width:0;
    height:0;
    border-left:13px solid transparent;
    border-right:13px solid transparent;
    border-bottom:13px solid #FFFFFF;
    margin-top: 2px;
    margin-left: 805px;
  }
  .shadow {
    position: absolute;
    background: rgba(1, 1, 1, 0.3);
    z-index: 1002;
    overflow: auto;
    top: 50px;
    left: 0;
    right: 15px;
    bottom: 0;
  }
  .menu-content {
    width: 825px;
    height: 500px;
    background-color: #FFFFFF;
    margin-top: -1px;
    float: right;
    /* margin-left: 15px; */
    border-radius: 6px;
  }
  .back-to-homepage {
    margin-left: 3.6%;
  }
  .back-to-homepage-title {
    color: #52AFEE;
    line-height: 25px;
    margin-left: 0.9%;
    margin-top: 2px;
    cursor: pointer;
  }
  .menu-content-item {
    display: flex;
    margin-top: 20px;
  }
  .menu-content-item-title {
    font-size: 18px;
    line-height: 25px;
  }
  .menu-content-item-left {
    width: 20%;
    margin-left: 3.5%;
  }
  .menu-content-item-left>img {
    width: 25px;
  }
  .menu-content-item-right {
    width: 80%;
  }
  .menu-content-item-right>div {
    width: 25%;
    float: left;
    color: #666666;
    font-size: 16px;
    line-height: 22px;
    margin-bottom: 20px;
    cursor: pointer;
  }
  .menu-content-item-right>div:hover {
    color: #4A90E2;
  }
}
@media screen and (min-width:633px) and (max-width:877px) {
  .fixed-menu {
    position: absolute;
    top: 70px;
    right: 3%;
    z-index: 1003;
  }
  .fixed-menu>img:hover {
    cursor: pointer;
  }
  .fixed-menu>img {
    margin-left: 880px;
  }
  .triangle {
    width:0;
    height:0;
    border-left:13px solid transparent;
    border-right:13px solid transparent;
    border-bottom:13px solid #FFFFFF;
    margin-top: 2px;
    margin-left: 885px;
  }
  .shadow {
    position: absolute;
    background: rgba(1, 1, 1, 0.3);
    z-index: 1002;
    overflow: auto;
    top: 50px;
    left: 0;
    right: 15px;
    bottom: 0;
  }
  .menu-content {
    width: 580px;
    height: 500px;
    background-color: #FFFFFF;
    margin-top: -1px;
    margin-left: 334px;
    border-radius: 6px;
  }
  .back-to-homepage {
    margin-left: 3.6%;
  }
  .back-to-homepage-title {
    color: #52AFEE;
    line-height: 25px;
    margin-left: 0.9%;
    margin-top: 2px;
    cursor: pointer;
  }
  .menu-content-item {
    display: flex;
    margin-top: 20px;
  }
  .menu-content-item-title {
    font-size: 16px;
    line-height: 25px;
  }
  .menu-content-item-left {
    width: 20%;
    margin-left: 3.5%;
  }
  .menu-content-item-left>img {
    width: 25px;
  }
  .menu-content-item-right {
    width: 80%;
  }
  .menu-content-item-right>div {
    width: 25%;
    float: left;
    color: #666666;
    font-size: 14px;
    line-height: 22px;
    margin-bottom: 20px;
    cursor: pointer;
  }
  .menu-content-item-right>div:hover {
    color: #4A90E2;
  }
}
@media screen and (max-width:622px) {
  .fixed-menu {
    position: absolute;
    top: 70px;
    right: 3%;
    z-index: 1003;
  }
  .fixed-menu>img:hover {
    cursor: pointer;
  }
  .fixed-menu>img {
    margin-left: 880px;
  }
  .triangle {
    width:0;
    height:0;
    border-left:13px solid transparent;
    border-right:13px solid transparent;
    border-bottom:13px solid #FFFFFF;
    margin-top: 2px;
    margin-left: 885px;
  }
  .shadow {
    position: absolute;
    background: rgba(1, 1, 1, 0.3);
    z-index: 1002;
    overflow: auto;
    top: 50px;
    left: 0;
    right: 15px;
    bottom: 0;
  }
  .menu-content {
    width: 350px;
    height: 700px;
    background-color: #FFFFFF;
    margin-top: -1px;
    margin-left: 562px;
    border-radius: 6px;
  }
  .back-to-homepage {
    margin-left: 3.6%;
  }
  .back-to-homepage-title {
    color: #52AFEE;
    line-height: 25px;
    margin-left: 0.9%;
    margin-top: 2px;
    cursor: pointer;
  }
  .menu-content-item {
    display: flex;
    margin-top: 20px;
  }
  .menu-content-item-title {
    font-size: 14px;
    line-height: 25px;
  }
  .menu-content-item-left {
    width: 30%;
    margin-left: 2%;
  }
  .menu-content-item-left>img {
    width: 20px;
  }
  .menu-content-item-right {
    width: 70%;
  }
  .menu-content-item-right>div {
    width: 50%;
    float: left;
    color: #666666;
    font-size: 12px;
    line-height: 22px;
    margin-bottom: 20px;
    cursor: pointer;
  }
  .menu-content-item-right>div:hover {
    color: #4A90E2;
  }
}
</style>

<template>
  <div class="layout" >
    <Layout>
      <Header>
        <Menu mode="horizontal" theme="dark"  active-name="1">
          <div class="layout-logo"></div>
          <div class="layout-nav">
            <MenuItem name="1">
              <Icon type="ios-navigate"></Icon>
              Item 1
            </MenuItem>
            <MenuItem name="2">
              <Icon type="ios-keypad"></Icon>
              Item 2
            </MenuItem>
            <MenuItem name="3">
              <Icon type="ios-analytics"></Icon>
              Item 3
            </MenuItem>
            <MenuItem name="4">
              <Icon type="ios-paper"></Icon>
              Item 4
            </MenuItem>
          </div>
        </Menu>
      </Header>
      <Layout>
        <Sider hide-trigger :style="{background: '#fff'}" >
          <div :style="{'height':getClientHeight}">
            <Menu width="200px" active-name="1-2" :open-names="['1']">
              <MenuItem name="1" @click.native ="mapWindowAutoLeft">
                <Icon type="ios-paper" />
                图层管理
              </MenuItem>
              <Submenu name="1">
                <template slot="title">
                  <Icon type="ios-paper" />
                  数据管理
                </template>
                <MenuItem name="1-1" @click.native = "handleTest">股票代码</MenuItem>
                <MenuItem name="1-2">评论管理</MenuItem>
                <MenuItem name="1-3">举报管理</MenuItem>
              </Submenu>
              <Submenu name="2">
                <template slot="title">
                  <Icon type="ios-people" />
                  用户管理
                </template>
                <MenuItem name="2-1">新增用户</MenuItem>
                <MenuItem name="2-2">更改密码</MenuItem>
              </Submenu>
            </Menu>
          </div>
        </Sider>
        <Layout :style="{padding: '0 5px 5px'}">
          <Content :style="{padding: '5px', minHeight: '480px', background: '#fff'}">
            <!--<component :is="currentView" keep-alive></component>-->
            <keep-alive>
              <router-view :is-active = "isActive" :is-active-statis = "isActiveStatis"></router-view>
            </keep-alive>
            <!-- 渲染组件 -->
            <!--<MapWindow v-bind:is-active="isActive"></MapWindow>-->
          </Content>
        </Layout>
      </Layout>
    </Layout>
  </div>
</template>
<script>
import StockCode from './manager/StockCode.vue'
export default {
  components: {StockCode},
  data: function () {
    return {
      isActive: true,
      isActiveStatis: true,
      currentView: null
    }
  },
  mounted: function () {
    this.mapWindowAutoLeft()
  },
  computed: {
    getClientHeight: function () {
      // 屏幕可视区域的高度
      let clientHeight = document.documentElement.clientHeight
      // 窗口可视区域发生变化的时候执行
      window.onresize = () => {
        clientHeight = document.documentElement.clientHeight
        return clientHeight
      }
      clientHeight = clientHeight - 100
      return clientHeight + 'px'
    }
  },
  methods: {
    mapWindowAutoLeft () {
      // this.$router.push({path: '/map'})
      this.isActive = !this.isActive
      /*      this.currentView = MapWindow
        this.isActive = !this.isActive */
    },
    handleTest () {
      this.$router.push({path: '/manager'})
      this.isActiveStatis = !this.isActiveStatis
      this.currentView = StockCode
    }
  }
}
</script>
<style scoped>
  .layout{
    border: 1px solid #d7dde4;
    background: #f5f7f9;
    position: relative;
    border-radius: 4px;
    overflow: hidden;
  }
  .layout-logo{
    width: 100px;
    height: 30px;
    background: #5b6270;
    border-radius: 3px;
    float: left;
    position: relative;
    top: 15px;
    left: 20px;
  }
  .layout-nav{
    width: 420px;
    margin: 0 auto;
    margin-right: 20px;
  }
</style>

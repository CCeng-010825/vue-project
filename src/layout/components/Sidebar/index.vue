<template>
  <div :class="{'has-logo':showLogo}">
    <logo v-if="showLogo" :collapse="isCollapse" />
    <el-scrollbar wrap-class="scrollbar-wrapper">
      <el-menu
        :default-active="activeMenu"
        :collapse="isCollapse"
        :background-color="variables.menuBg"
        :text-color="variables.menuText"
        :unique-opened="false"
        :active-text-color="variables.menuActiveText"
        :collapse-transition="false"
        mode="vertical"
      >
        <sidebar-item v-for="route in routes" :key="route.path" :item="route" :base-path="route.path" />
      </el-menu>
    </el-scrollbar>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import Logo from './Logo'
import SidebarItem from './SidebarItem'
import variables from '@/styles/variables.scss'
// import { computed, reactive } from 'vue'

export default {
  components: { SidebarItem, Logo },
  data() {
    return {
      // activeMenu: ''
    }
  },
  computed: {
    ...mapGetters([
      'sidebar'
    ]),
    routes() {
      // return this.$router.options.routes
      return this.$store.state.user.resultAllRoutes
    },
    activeMenu() {
      const route = this.$route
      const { meta, path } = route
      console.log(meta, 'meat')
      // if set path, the sidebar will highlight the path you set
      if (meta.activeMenu) {
        console.log(meta.activeMenu, 'ppppp')
        return meta.activeMenu
      }
      console.log(path, 'path')
      return path
    },
    // const activeMenu=computed(()=>{
    //   const route=this.$route
    //   const {meta,path}=route
    //   if(meta.activeMenu){
    //     return meta.activeMenu
    //   }
    //   return path
    // }),
    showLogo() {
      return this.$store.state.settings.sidebarLogo
    },
    variables() {
      return variables
    },
    isCollapse() {
      return !this.sidebar.opened
    }
  }
}
</script>

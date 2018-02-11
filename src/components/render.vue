<template>
  <div @click="blurRenderItem" @click.stop.prevent="editRenderItem(render.config)" class="render-container">
    <div drag-tag="modules" :class="{'active': activeModules, 'current': currentModule === render.config}" class="body" :style="customStyle" ref="renderBody">
      <div class="item" :key="item._timestamp" :class="{'current': currentModule === item}" v-for="(item, index) in items">
        <dragMove :class="{'current': currentModule === item}" :drag="true" restriction=".component" @update="updateStyle(item, $event)" :p-style="item.style" :allowKeyMove="item === currentModule">
          <ctrl-bar :hide-sort="true" v-show="currentModule === item" :items="items" :item="item"></ctrl-bar>
          <component :data="item.data" :is="components[item.type]" @mousedown="editRenderItem(item)"></component>
        </dragMove>
      </div>
    </div>
    <drag-drop></drag-drop>
  </div>
</template>
<style lang="less">
  @import '~_base.less';

  .dropArea {
    width: 95%;
    height: 2px;
    line-height: 0;
    font-size: 0;
    margin: 0 auto;
    display: block;
    content: '';
    border: 4px solid;
    border-color: transparent #2196F3;
    background: #2196F3;
    background-clip: content-box;
  }
  .render-container {
    // overflow-y: auto;
    height:100%;
    .body {
      width: 80%;
      // height: 10px;
      // min-height: 667px;
      // padding-top: 64px;
      margin: 20px auto 30px;
      user-select: none;
      box-shadow: 0 0 30px 0 rgba(0, 0, 0, 0.30);
      position: relative;
      background-repeat: no-repeat;
      background-position: center top;
      background-origin: content-box;
      background-size: 100% auto;
      .phone-head {
        position: absolute;
        width: 100%;
        top: 0;
        left: 0;
        background: #fff url("../assets/img/phone-head.png") no-repeat;
        height: 64px;
      }
      .page-title {
        color: #fff;
        border: none;
        font-size: 20px;
        text-align: center;
        position: absolute;
        top: 27px;
        left: 73px;
        width: 210px;
        background: transparent;
      }
      .page-config {
        cursor: pointer;
        font-size: 18px;
        position: absolute;
        top: 27px;
        right: 10px;
        text-align: center;
        width: 25px;
        height: 25px;
        line-height: 25px;
        color: #fff;
      }
      &.current {
        outline: 2px solid #2196F3;
      }
      &.active {
        outline: 2px solid #2196F3;
        &:after:extend(.dropArea) {
          //
        }
      }
      .item {
        cursor: pointer;
        position: relative;
        &.current {
          // outline: 2px solid #2196F3;
          z-index: 998;
        }
        .placeholder {
          position: relative;
          &:before {
            content: '';
            position: absolute;
            top: 0;
            bottom: 0;
            left: 0;
            right: 0;
            opacity: 0.4;
            background: url("data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSI3MCIgaGVpZ2h0PSI3MCI+CjxyZWN0IHdpZHRoPSI3MCIgaGVpZ2h0PSI3MCIgZmlsbD0iI2ZmZiI+PC9yZWN0Pgo8ZyB0cmFuc2Zvcm09InJvdGF0ZSg0NSkiPgo8cmVjdCB3aWR0aD0iOTkiIGhlaWdodD0iMjUiIGZpbGw9IiNlZWUiPjwvcmVjdD4KPHJlY3QgeT0iLTUwIiB3aWR0aD0iOTkiIGhlaWdodD0iMjUiIGZpbGw9IiNlZWUiPjwvcmVjdD4KPC9nPgo8L3N2Zz4=");
          }
        }
      }
      .component {
        position: absolute;
        &.active.top {
          &:before:extend(.dropArea) {
            //
          }
        }
        &.active.inner {
          outline: 2px solid #2196F3;
          background-color: #E4F3FE;
        }
        &.active.bottom {
          &:after:extend(.dropArea) {
            //
          }
        }
      }
    }
  }

</style>
<script>
import { mapGetters, mapActions } from 'vuex'
import {
  components,
  modules
} from '../modules'
import dragDrop from './drag-drop.vue'
import dragMove from './drag-move.vue'
import ctrlBar from './ctrl-bar.vue'
import { createStyles } from '../utils'

export default {
  components: {
    ...components,
    dragDrop,
    ctrlBar,
    dragMove
  },

  mounted() {
    // 模拟 rem
    setFontSize()

    function setFontSize() {
      document.documentElement.style.fontSize = '20px'
    }
    this.$nextTick(() => {
      this.$refs.renderBody.style.height = this.$refs.renderBody.offsetWidth*9/16+'px'
    })
  },

  watch: {
    'base.activeDocumentTitle' (newVal) {
      this.$refs.documentTitle.focus()
      this.base.activeDocumentTitle = false
    }
  },

  computed: {
    ...mapGetters({
      base: 'base',
      render: 'render',
      items: 'renderItems',
      activeModules: 'activeModules',
      activeModule: 'activeModule',
      currentModule: 'currentModule'
    }),
    customStyle() {
      console.log(this.render.config)
      return createStyles(this.render.config)
    // },
    // renderHeight() {
    //   console.log(this.$refs);
    //   return this.$refs.renderBody.style.width*9/16
    }
  },

  methods: {
    ...mapActions([
      'editRenderItem',
      'blurRenderItem',
      'editDragModule',
      'editItemStyle'
    ]),
    drag(item) {
      var index = this.items.indexOf(item);
      this.items.splice(index, 1)
      this.editDragModule(item)
    },
    onSort(newIndex, oldIndex) {
      setTimeout(() => {
        this.editRenderItem(this.items[newIndex]);
        //this.$store.commit('EDIT_RENDER_ITEM', this.items[newIndex])
      }, 0)
    },
    updateStyle(item, position) {
      // if(item.$treeNodeId)localStorage[item.$treeNodeId] = position
      this.editItemStyle(item.$treeNodeId,position)
      item.style = {
        ...item.style,
        ...position
      }
    }
  },

  data() {
    return {
      modules,
      components
    }
  }
}
</script>

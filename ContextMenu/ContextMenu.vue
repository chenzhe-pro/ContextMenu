<template>
  <ul
    class="contextmenu"
    :style="{
      left: menuStyle.left + 'px',
      top: menuStyle.top + 'px',
      width: width + 'px'
    }"
  >
    <li
      v-for="(item, index) in menu"
      class="contextmenu__item"
      :key="item.action || item.name"
      :id="item.action || item.name"
    >
      <div @click.stop="fnHandler(item)" class="button">
        <i v-if="icon" :class="item.icon"></i>
        <span>{{ item.name }}</span>
        <i
          class="el-icon-arrow-right"
          v-if="item.children && item.children.length > 0"
        ></i>
        <context-menu
          v-if="item.children && item.children.length > 0"
          :menu="item.children"
          :icon="icon"
          :current-pos="menuStyle"
          :parent-index="index"
          :width="width"
          :resolve="resolve"
        ></context-menu>
      </div>
    </li>
  </ul>
</template>

<script>
export default {
  name: 'context-menu',
  props: {
    // customEvent: {
    //   type: Object
    // },
    icon: {
      // 是否显示icon
      type: Boolean,
      default: true
    },
    menu: {
      // 最重要的列表，没有的话直接不显示
      type: Array,
      default() {
        return [
          // { icon: '', name: '', action: '', fn: function() {} },
          // 模板，必须有name是国际化传过来, action是作为key和action的存在, icon如果显示但不传icon的话会留空白
          // { icon: 'el-icon-view', name: '查看', action: 'view', fn: function() {} },
          // {icon: 'el-icon-edit', name: '编辑', action: 'edit'},
          // {icon: 'el-icon-setting', name: '设置', action: 'setting'}
          // { icon: 'el-icon-delete', name: '删除', action: 'delete' }, // 此处传入参数时记得国际化
          // { icon: 'el-icon-printer', name: '打印', action: 'print' },
        ];
      }
    },
    width: {
      // 菜单宽度
      type: Number
    },
    resolve: {
      // 点击menu按钮时执行的方法
      type: Function
    },
    currentPos: {
      type: Object
    },
    relativePos: {
      type: Object,
      default: function() {
        return { left: 0, top: 0 };
      }
    },
    first: {
      type: Boolean,
      default: function() {
        return false;
      }
    },
    parentIndex: {
      type: Number,
      default() {
        return 0;
      }
    }
    // reject: { // 不点击按钮点击其他地方关闭时执行的方法 .catch(e => {})
    //   type: Function,
    //   default: function() {}
    // }
  },
  data() {
    return {
      status: false
    };
  },
  computed: {
    menuStyle() {
      let position = {};
      let menuHeight = this.menu.length * 30;
      let menuWidth = this.width;
      if (this.first) {
        return this.currentPos;
      }
      if (document.body.clientWidth < this.currentPos.left + menuWidth * 2) {
        position.left =
          this.currentPos.left +
          menuWidth -
          5 -
          (this.currentPos.left + menuWidth * 2 - document.body.clientWidth);
      } else {
        position.left = this.currentPos.left + menuWidth - 2;
      }
      if (
        document.body.clientHeight <
        this.currentPos.top + menuHeight + this.parentIndex * 30
      ) {
        position.top =
          this.currentPos.top +
          this.parentIndex * 30 -
          5 -
          (this.currentPos.top +
            menuHeight +
            this.parentIndex * 30 -
            document.body.clientHeight);
      } else {
        position.top = this.currentPos.top + this.parentIndex * 30;
      }
      return position;
    }
  },
  methods: {
    fnHandler(item) {
      if (item.children && item.children.length > 0) {
        return false;
      }
      this.status = false;
      // if (item.fn) item.fn(this.customEvent);
      this.resolve(item.action);
    }
  },
  beforeDestroy() {
    document.body.removeChild(this.$el);
  },
  mounted() {
    // 挂载后才开始计算左右，隐藏挂载后显示不会闪一下
    // this.$nextTick(function () {
    //   this.status = true;
    // });
  },
  components: {}
};
</script>

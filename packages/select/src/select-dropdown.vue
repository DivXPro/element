<template>
  <div
    class="el-select-dropdown el-popper"
    :class="[{ 'is-multiple': $parent.multiple }, popperClass]"
    :style="{ minWidth: minWidth }">
    <el-input 
      v-if="filterable"
      ref="input"
      prefix-icon="el-icon-search"
      @keyup.native="debouncedOnInputChange"
      @paste.native="debouncedOnInputChange"
      v-model="key"></el-input>
    <el-tree v-if="tree" :data="treeData" :props="treeProps" @node-click="handleNodeClick"></el-tree>
    <slot></slot>
  </div>
</template>

<script type="text/babel">
  import debounce from 'throttle-debounce/debounce';
  import Popper from 'element-ui/src/utils/vue-popper';
  import Emitter from 'element-ui/src/mixins/emitter';
  import Focus from 'element-ui/src/mixins/focus';
  import ElInput from 'element-ui/packages/input';
  import Tree from 'element-ui/packages/tree';

  export default {
    name: 'ElSelectDropdown',

    componentName: 'ElSelectDropdown',

    mixins: [Popper, Emitter, Focus('input')],

    props: {
      placement: {
        default: 'bottom-start'
      },

      boundariesPadding: {
        default: 0
      },

      popperOptions: {
        default() {
          return {
            gpuAcceleration: false
          };
        }
      },

      visibleArrow: {
        default: true
      },

      appendToBody: {
        type: Boolean,
        default: true
      }
    },

    data() {
      return {
        key: '',
        minWidth: ''
      };
    },

    created() {
      this.debouncedOnInputChange = debounce(this.debounce, () => {
        this.dispatch('ElSelect', 'setQuery', [this.key]);
      });
    },

    computed: {
      popperClass() {
        return this.$parent.popperClass;
      },

      debounce() {
        return this.$parent.remote ? 300 : 0;
      },

      filterable() {
        return this.$parent.filterable && !this.$parent.allowCreate;
      },

      tree() {
        return this.$parent.tree;
      },

      treeData() {
        return this.$parent.treeData;
      },

      treeProps() {
        return this.$parent.treeProps;
      }
    },

    watch: {
      '$parent.inputWidth'() {
        this.minWidth = this.$parent.$el.getBoundingClientRect().width + 'px';
      }
    },

    mounted() {
      this.referenceElm = this.$parent.$refs.reference.$el;
      this.$parent.popperElm = this.popperElm = this.$el;
      this.$on('updatePopper', () => {
        if (this.$parent.visible) {
          this.updatePopper();
          if (this.filterable) {
            setTimeout(() => {
              this.$refs.input.focus();
            }, 500);
          }
        }
      });
      this.$on('destroyPopper', () => {
        this.destroyPopper();
      });
    },

    methods: {
      handleNodeClick({ value }) {
        this.dispatch('ElSelect', 'handleOptionClick', [{ value }, true]);
      }
    },
  
    components: {
      ElInput,
      Tree
    }
  };
</script>

<template>
  <div
    class="el-select-dropdown el-popper"
    :class="[{ 'is-multiple': $parent.multiple }, popperClass]"
    :style="{ minWidth: minWidth }">
    <el-input 
      prefix-icon="el-icon-search"
      @keyup.native="debouncedOnInputChange"
      @paste.native="debouncedOnInputChange"
      v-model="key"></el-input>
    <slot></slot>
  </div>
</template>

<script type="text/babel">
  import debounce from 'throttle-debounce/debounce';
  import Popper from 'element-ui/src/utils/vue-popper';
  import Emitter from 'element-ui/src/mixins/emitter';
  import ElInput from 'element-ui/packages/input';

  export default {
    name: 'ElSelectDropdown',

    componentName: 'ElSelectDropdown',

    mixins: [Popper, Emitter],

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
        if (this.$parent.visible) this.updatePopper();
      });
      this.$on('destroyPopper', this.destroyPopper);
    },
  
    components: {
      ElInput
    }
  };
</script>
<style lang="scss">
  .el-select-dropdown {
    .el-input__inner {
      border-bottom-left-radius: 0;
      border-bottom-right-radius: 0;
      border: none;
      border-bottom: 1px solid #e7eaf1;
    }
  }
</style>

<template>
  <div class="g-nav" :class="{vertical}">
    <slot></slot>
  </div>
</template>
<script>
export default {
  name: "GuluNav",
  provide() {
    return {
      root: this
    };
  },
  props: {
    selected: {
      type: Array,
      default: () => []
    },
    trigger: {
      type: String,
      default: "click",
      validator(val) {
        return ["click", "hover"].indexOf(val) >= 0;
      }
    },
    vertical: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      items: [],
      namePath: []
    };
  },
  mounted() {
    this.updatedChildren();
    this.listenToChilderen();
  },
  watch: {
    selected: function() {
      this.updatedChildren();
    }
  },
  computed: {},
  methods: {
    addItem(vm) {
      this.items.push(vm);
    },
    updatedChildren() {
      this.items.forEach(vm => {
        if (this.selected.indexOf(vm.name) >= 0) {
          vm.selected = true;
          this.namePath = [];
          vm.$parent.updateNamePath && vm.$parent.updateNamePath();
          vm.$parent.closeAll && vm.$parent.closeAll();
        } else {
          vm.selected = false;
        }
      });
    },
    listenToChilderen() {
      this.items.forEach(vm => {
        vm.$on("update:selected", name => {
          this.$emit("update:selected", [name]);
        });
      });
    }
  }
};
</script>
<style lang="scss" scoped>
@import "./../../styles/var";
.g-nav {
  display: flex;
  border-bottom: 1px solid $grey;
  cursor: default;
  user-select: none;
  &.vertical {
    flex-direction: column;
  }
}
</style>

<template>
  <li class="treeview">
    <div :class="{ bold: isFolder }" v-if="model">
      <span v-if="isFolder" class="toggle" :class="{ expanded }" @click="toggle">
        <b-icon icon="expand_more"></b-icon>
      </span>
      <span v-else @click="toggle">
        <b-icon icon="bubble_chart"></b-icon>
      </span>
      <span class="key" @click="toggle">{{ model.name }}</span>
      <code class="value" v-if="!isFolder">{{ model.val }}</code>
    </div>
    <ul v-show="expanded" v-if="isFolder">
      <tree class="item" v-for="child in model.children"
        :ref="id(child)" :key="child.name" :model="child">
      </tree>
    </ul>

  </li>
</template>

<script>
export default {
  name: 'tree',
  props: {
    model: Object,
    open: Boolean,
  },
  data() {
    return { expanded: this.open }
  },
  computed: {
    isFolder() {
      return this.model.children && this.model.children.length
    },
  },
  methods: {
    id(node) {
      return 'child_' + node.name
    },
    toggle() {
      if (this.isFolder) {
        this.expanded = !this.expanded
      }
    },
    toggleAll(status) {
      this.expanded = status
      if (this.isFolder) {
        this.model.children.forEach(child => {
          let list = this.$refs[`child_` + child.name]
          if (list && list.length) {
            let [vm, ] = list
            vm.toggleAll(status)
          }
        })
      }
    },
  }
}
</script>

<style lang="scss">
.treeview {
  list-style: none;

  .key::after {
    content: ":";
    font-family: monospace;
  }
  .bold {
    font-weight: bold;
    .key,
    .toggle {
      cursor: pointer;
    }

    .toggle {
      .icon {
        transform: rotate(-90deg);
        transition: transform 0.2s ease-in-out;
      }
      &.expanded .icon {
        transform: rotate(0);
      }
    }
  }
  ul {
    margin: 0;
    padding-left: 1.5em;
    line-height: 1.5em;
  }
}
</style>

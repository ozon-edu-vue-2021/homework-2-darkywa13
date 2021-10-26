<template>
  <li :class="classes">
    <button
    type="button"
    class="tree-item__action"
    @focus="onFocus"
    @blur="onBlur"
    @click="toggle"
    @keypress.enter="onEnter"
  >
    <span>{{ itemName }}</span>
  </button>

    <div v-if="(hasContents && isChildrenOpen) || childrenIsMounted" v-show="isChildrenOpen" class="tree-item__children">
      <slot />
    </div>
  </li>
</template>

<script>
export default {
  name: 'TreeItem',
  props: {
    itemData: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      isChildrenOpen: false,
      childrenIsMounted: false,
    }
  },
  computed: {
    hasContents() {
      return Array.isArray(this.itemData.contents)
    },
    itemName() {
      return this.itemData.name
    },
    classes() {
      return [
        'tree-item',
        `tree-item_type_${this.itemData.type}`,
        {
          'tree-item_is-children-open': this.isChildrenOpen,
        }
      ]
    }
  },
  methods: {
    toggle($e) {
      if (this.hasContents) {
        this.isChildrenOpen = !this.isChildrenOpen

        if (!this.childrenIsMounted) {
          this.$nextTick(() => {
            this.childrenIsMounted = true
          })
        }
      }

      // safari focus fix
      if (document.activeElement !== $e.currentTarget) {
        $e.currentTarget.focus()
      }
    },
    onFocus() {
      this.$emit('on-path', [])
    },
    onBlur() {
      this.$emit('on-path', null)
    },
    onEnter() {
      if (!this.hasContents) {
        alert('This is the File')
      }
    }
  }
}
</script>

<style lang="scss" scoped>
  $bg-color-item-focus: #717198;
  $text-color-item-focus: #FFFFFF;

  $icon-directory: '\1F4C1';
  $icon-directory-open: '\1F4C2';
  $icon-file: '\1F4C4';
  $icon-link: '\1F517';

  .tree-item {
    font-size: 16px;
    margin-bottom: 10px;
    list-style: none;

    &:last-child {
      margin-bottom: 0;
    }

    &__action {
      display: inline-block;
      background-color: transparent;
      border: none;
      padding: 0;

      &:hover {
        cursor: pointer;
      }

      &:focus {
        background: $bg-color-item-focus;
        color: $text-color-item-focus;
      }

      &:before {
        content: '';
        margin-right: 5px;
      }
    }

    &__children {
      margin-top: 10px;
    }

    &_type {
      &_directory > {
        .tree-item__action {
          font-weight: bold;

          &:before {
            content: $icon-directory;
          }
        }
      }
      
      &_file > {
        .tree-item__action:before {
          content: $icon-file;
        }
      }
      
      &_link > {
        .tree-item__action:before {
          content: $icon-link;
        }
      }
    }

    &_is-children-open {
      &.tree-item_type_directory > .tree-item__action:before {
        content: $icon-directory-open;
      }
    }
  }
</style>

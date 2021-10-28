<template>
  <ul :class="['tree-list', { 'tree-list_root': root }]">
    <tree-item
      v-for="(item, index) in list"
      :key="index"
      :item-data="item"
      @on-path="($e) => setPath($e, item)"
    >
      <tree-list v-if="item.contents" :list-data="item.contents" @on-path="($e) => setPath($e, item)" />
    </tree-item>
  </ul>
</template>

<script>
import TreeItem from './TreeItem.vue'

export default {
  name: 'TreeList',
  components: {
    TreeItem
  },
  props: {
    listData: {
      type: [Array, Object],
      required: true,
    },
    root: Boolean
  },
  data() {
    return {
      list: [],
      // пауза в обработке данных
      processTimeout: 50,
      // кол-во итемов в пачке для обработки
      processMaxItemsLenPerPart: 50,
      // условное кол-во мс для фриза
      processLag: 50
    }
  },
  created() {
    const list = Array.isArray(this.listData) ? this.listData : [this.listData]
    const todo = list.concat()

    setTimeout(this.updateList.bind(this, todo), this.processTimeout)
  },
  methods: {
    /**
     * Оптимизация обработки данных больших массивов.
     * При необходимости, можно сделать индикатор обработки
     * и показывать юзеру где / что / сколько уже было обработано.
     */
    updateList(todo) {
      if (todo.length === 0) {
        return
      }

      const start = +new Date()
      let counter = 0

      do {
        this.list.push(todo.shift())
        counter++
      } while (todo.length > 0 && (+new Date() - start < this.processLag) && counter < this.processMaxItemsLenPerPart)

      if (todo.length > 0) {
        setTimeout(this.updateList.bind(this, todo), this.processTimeout)
      }
    },
    setPath($e, item) {
      if ($e) {
        $e.unshift(item.name)
        this.$emit('on-path', $e)
      } else {
        this.$emit('on-path', null)
      }
    }
  }
}
</script>

<style lang="scss" scoped>
  $list-border-color: #424220;
  
  .tree-list {
    &:not(.tree-list_root) {
      padding-left: 25px;
      border-left: 1px dotted $list-border-color;
      list-style-position: inside;
    }

    &_root {
      padding: 0;
      margin: 0;
    }
  }

  @media screen and (max-width: 768px) {

  }
</style>

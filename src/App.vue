<template>
  <div id="app">
    <div class="wrapper">
      <div class="tree">
        <tree-list :list-data="listData" root @on-path="setPath" />
      </div>
      <div v-if="path" class="content">
        <h3>{{ title }}</h3>
        <div class="content__path">{{ path }}</div>
      </div>
    </div>
  </div>
</template>

<script>
import listData from '../public/static/node_modules.json'
import TreeList from './components/TreeList.vue'

export default {
  name: 'App',
  components: {
    TreeList
  },
  data() {
    return {
      path: '',
      title: 'Путь к файлу / папке:',
      listData
    }
  },
  methods: {
    setPath($e) {
      this.path = $e ? $e.join(' > ') : ''
    }
  }
}
</script>

<style lang="scss" scoped>
  $bg-color-app: #f5f5f5;
  $content-border-color: #cccccc;

  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    color: #2c3e50;
    margin: 50px;
    background-color: $bg-color-app;
  }

  .wrapper {
    display: flex;
    height: calc(100vh - 140px);
    padding: 20px;
  }

  .tree {
    flex: 1;
    max-height: 100%;
    overflow: auto;
  }

  .content {
    width: 50%;
    border-left: 1px solid $content-border-color;
    text-align: center;
    padding: 20px;
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  @media screen and (max-width: 768px) {
    #app {
      margin: 20px;
    }

    .wrapper {
      flex-direction: column-reverse;
      padding: 10px;
    }

    .content {
      width: 100%;
      margin-bottom: 20px;
      border-left: none;
      padding: 0;
    }
  }
</style>

<template>
  <div class="search-container">
    <!-- 搜索栏 -->
    <form action="/" class="search-form">
      <van-search v-model="searchText"
                  show-action
                  placeholder="请输入搜索关键词"
                  background="#3296fa"
                  @search="onSearch"
                  @focus="isResultShow = false"
                  @cancel="onCancel" />
    </form>
    <!-- 搜索结果 -->
    <search-result v-if="isResultShow" :search-text="searchText" />
    <!-- 联想建议 -->
    <search-suggestion v-else-if="searchText" :search-text="searchText" @search="onSearch" />
    <!-- 搜索历史记录 -->
    <search-history v-else :search-histories="SearchHistories" @clear-search-histories="SearchHistories = []"
    @search="onSearch" />
  </div>
</template>

<script>
import SearchHistory from './components/search-history'
import SearchSuggestion from './components/search-suggestion'
import SearchResult from './components/search-result'
import { setItem, getItem } from '@/utils/storage'
export default {
  name: 'earchIndex',
  data () {
    return {
      searchText: '',
      isResultShow: false, // 控制搜索结果的展示
      SearchHistories: getItem('TOUTIAO_SEARCH_HISTORIES') || []
    }
  },
  components: {
    SearchHistory,
    SearchSuggestion,
    SearchResult
  },
  methods: {
    onSearch (val) {
      // 更新文本框内容
      this.searchText = val
      // 存储历史记录
      const index = this.SearchHistories.indexOf(val)
      if (index !== -1) {
        this.SearchHistories.splice(index, 1)
      }
      this.SearchHistories.unshift(val)
      // 展示搜索结果
      this.isResultShow = true
    },
    onCancel () {
      this.$router.back()
    }
  },
  watch: {
    SearchHistories (value) {
      setItem('TOUTIAO_SEARCH_HISTORIES', value)
    }
  }
}
</script>

<style lang="less" scoped>
.search-container {
  padding-top: 108px;
  .van-search__action {
    color: #fff;
  }
  .search-form {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    z-index: 1;
  }
}
</style>

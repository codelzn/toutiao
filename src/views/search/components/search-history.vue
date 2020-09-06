<template>
  <div class="search-history">
    <van-cell title="搜索历史">
      <div v-if="isDeleteShow">
        <span @click="$emit('clear-search-histories')">全部删除</span>
        &nbsp;&nbsp;
        <span @click="isDeleteShow = false">完成</span>
      </div>
      <van-icon @click="isDeleteShow = true" v-else name="delete" />
    </van-cell>
    <van-cell v-for="(item,index) in searchHistories"
              :key="index"
              :title="item"
              @click="onSearchItemClick(item, index)">
      <van-icon v-if="isDeleteShow"
                name="close" />
    </van-cell>

  </div>
</template>

<script>
export default {
  name: 'SearchHistory',
  props: {
    searchHistories: {
      type: Array,
      required: true
    }
  },
  data () {
    return {
      isDeleteShow: false
    }
  },
  methods: {
    onSearchItemClick (item, index) {
      if (this.isDeleteShow) {
        // 删除状态
        this.searchHistories.splice(index, 1)
      } else {
        // 非删除状态 进入搜索
        this.$emit('search', item)
      }
    }
  }
}
</script>

<style lang="less" scoped>
</style>

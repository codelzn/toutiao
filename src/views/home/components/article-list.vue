<template>
  <div class="article-list">
    <van-pull-refresh v-model="isreFreshLoading"
                      @refresh="onRefresh"
                      :success-text="rst"
                      success-duration="1500">
      <van-list v-model="loading"
                :finished="finished"
                finished-text="没有更多了"
                @load="onLoad"
                :error.sync="error"
                error-text="请求失败，点击重新加载">
        <article-item
          v-for="(article, index) in list"
                  :key="index"
                  :article="article"
         />
      </van-list>
    </van-pull-refresh>
  </div>
</template>

<script>
import { getArticles } from '@/api/article'
import ArticleItem from '@/components/article-item'
export default {
  name: 'ArticleList',
  components: {
    ArticleItem
  },
  props: {
    channel: {
      type: Object,
      required: true
    }
  },
  data () {
    return {
      list: [],
      loading: false,
      finished: false,
      timestamp: null,
      error: false,
      isreFreshLoading: false,
      rst: '刷新成功'
    }
  },
  methods: {
    async onLoad () {
      try {
        const { data } = await getArticles({
          channel_id: this.channel.id,
          // 数据的页码 传当前时间戳
          timestamp: this.timestamp || Date.now(),
          with_top: 1
        })
        const { results } = data.data
        this.list.push(...results)
        this.loading = false
        // 判断数据是否全部加载完成
        if (results.length) {
          // 更新时间戳
          this.timestamp = data.data.pre_timestamp
        } else {
          this.finished = true
        }
      } catch (err) {
        this.error = true
      }
    },
    // 下拉刷新
    async onRefresh () {
      try {
        const { data } = await getArticles({
          channel_id: this.channel.id,
          // 数据的页码 传当前时间戳
          timestamp: Date.now(),
          with_top: 1
        })
        const { results } = data.data
        this.list.unshift(...results)
        this.isreFreshLoading = false
        this.rst = `刷新成功,更新了${results.length}条数据`
      } catch (err) {
        this.rst = '刷新失败'
        this.isreFreshLoading = false
      }
    }
  }
}
</script>

<style lang="less" scoped>
.article-list {
  overflow-y: auto;
  height: 79vh;
}
</style>

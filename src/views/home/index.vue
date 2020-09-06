<template>
  <div class="home-container">
    <!-- 导航栏 -->
    <van-nav-bar class="page-nav-bar"
                 fixed>
      <van-button class="search-btn"
                  slot="title"
                  type="info"
                  size="small"
                  round
                  to="/search"
                  icon="search">搜索</van-button>
    </van-nav-bar>
    <!-- 频道列表 -->
    <van-tabs class="channel-tabs"
              v-model="active"
              animated>
      <van-tab v-for="channel in channels"
               :key="channel.id"
               :title="channel.name">
        <article-list :channel="channel" />
      </van-tab>
      <div slot="nav-right"
           class="placeholder"></div>
      <div slot="nav-right"
           class="hamburger-btn"
           @click="isChennelEditShow = true">
        <i class="toutiao toutiao-gengduo"></i>
      </div>
    </van-tabs>
    <!-- 频道编辑弹出层 -->
    <van-popup v-model="isChennelEditShow"
               closeable
               close-icon-position="top-left"
               position="bottom"
               :style="{ height: '100%' }">
               <channel-edit
               :my-channels="channels"
               :active="active"
               @update-active="onUpdateActive" />
    </van-popup>
  </div>
</template>

<script>
import { getUserChannels } from '@/api/user'
import ArticleList from './components/article-list'
import ChannelEdit from './components/channel-edit'
import { mapState } from 'vuex'
import { getItem } from '@/utils/storage'
export default {
  name: 'HomeIndex',
  components: {
    ArticleList,
    ChannelEdit
  },
  data () {
    return {
      active: 0,
      channels: [], // 频道列表
      isChennelEditShow: false
    }
  },
  computed: {
    ...mapState(['user'])
  },
  methods: {
    async loadChannels () {
      try {
        let channels = []
        if (this.user) {
          // 已登录
          const { data } = await getUserChannels()
          channels = data.data.channels
        } else {
          // 未登录
          const localChannels = getItem('TOUTIAO_CHANNELS')
          if (localChannels) {
            // 有本地存储
            channels = localChannels
          } else {
            // 没有本地存储 请求获取默认数据
            const { data } = await getUserChannels()
            channels = data.data.channels
          }
        }
        this.channels = channels
      } catch (err) {
        this.$toast('获取频道数据失败')
      }
    },
    onUpdateActive (index, isChennelEditShow = true) {
      this.active = index
      this.isChennelEditShow = isChennelEditShow
    }
  },
  mounted () {
    window.home = this
  },
  created () {
    this.loadChannels()
  }
}
</script>

<style lang="less" scoped>
.home-container {
  padding-top: 174px;
  padding-bottom: 100px;
  /deep/ .van-nav-bar__title {
    max-width: unset;
  }
  .search-btn {
    width: 555px;
    height: 64px;
    background-color: #5babfb;
    border: none;
    font-size: 28px;
    .van-icon {
      font-size: 32px;
    }
  }
  /deep/ .channel-tabs {
    .van-tabs__wrap {
      position: fixed;
      top: 92px;
      z-index: 1;
      left: 0;
      right: 0;
      height: 82px;
    }
    .van-tab {
      min-width: 200px;
      border-right: 1px solid #edeff3;
      font-size: 30px;
      color: #777;
    }
    .van-tab--active {
      color: #333;
    }
    .van-tabs__nav {
      padding-bottom: 0;
    }
    .van-tabs__line {
      bottom: 8px;
      width: 31px !important;
      height: 6px;
      background-color: #3296fa;
    }
    .placeholder {
      // 不参与剩余空间计算
      flex-shrink: 0;
      width: 66px;
      height: 82px;
    }
    .hamburger-btn {
      position: fixed;
      right: 0;
      width: 66px;
      height: 82px;
      display: flex;
      justify-content: center;
      align-items: center;
      background-color: #fff;
      opacity: 0.902;
      i.toutiao {
        font-size: 33px;
      }
      &::before {
        content: "";
        width: 1px;
        height: 100%;
        background: url(~@/assets/gradient-gray-line.png);
        position: absolute;
        left: 0;
        background-size: contain;
      }
    }
  }
}
</style>

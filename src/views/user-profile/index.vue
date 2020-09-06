<template>
  <div class="user-profile">
    <!-- 导航栏 -->
    <van-nav-bar class="page-nav-bar" title="个人信息" left-arrow @click-left="$router.back()" />
    <!-- 个人信息 -->
    <van-cell title="头像" is-link>
      <van-image
        class="avatar"
        fit="cover"
        round
        :src="user.photo"
      />
    </van-cell>
    <van-cell @click="isUpdateNameShow = true" title="昵称" :value="user.name" is-link />
    <van-cell @click="isUpdateGenderShow = true" title="性别" :value="user.gender === 0 ? '男' : '女'" is-link />
    <van-cell title="生日" :value="user.birthday" is-link />
    <!-- 编辑昵称 -->
    <van-popup position="bottom" v-model="isUpdateNameShow" style="height: 100%;">
      <update-name v-if="isUpdateNameShow" v-model="user.name" @close="isUpdateNameShow = false" />
    </van-popup>
    <!-- 编辑性别 -->
    <van-popup position="bottom" v-model="isUpdateGenderShow">
      <update-gender v-if="isUpdateGenderShow" @close="isUpdateGenderShow = false" v-model="user.gender" />
    </van-popup>
  </div>
</template>

<script>
import { getUserProfile } from '@/api/user'
import UpdateName from './components/update-name'
import UpdateGender from './components/update-gender'
export default {
  name: 'UserProfile',
  components: {
    UpdateName,
    UpdateGender
  },
  data () {
    return {
      user: {}, // 个人信息
      isUpdateNameShow: false, // 编辑昵称
      isUpdateGenderShow: false // 编辑性别
    }
  },
  methods: {
    async loadUserProfile () {
      try {
        const { data } = await getUserProfile()
        this.user = data.data
      } catch (err) {
        this.$toast('获取数据失败')
      }
    }
  },
  created () {
    this.loadUserProfile()
  }
}
</script>

<style lang="less" scoped>
.user-profile {
  .avatar {
    width: 60px;
    height: 60px;
  }
  .van-popup {
    background-color: #f5f7f9;
  }
}
</style>

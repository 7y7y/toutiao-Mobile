<template>
  <div class='container'>
     <van-nav-bar fixed title="搜索结果" left-arrow @click-left="$router.back()" />
       <!-- 实现上拉加载 -->
      <van-list v-model="loading" @load="onLoad" :finished="finished">
        <van-cell-group>
          <!-- 注册点击事件跳转详情页 artId传参 -->
          <van-cell :to="`/article?artId=${item.art_id.toString()}`" v-for="item in articles" :key="item.art_id.toString()">
            <div class="article_item">
              <h3 class="van-ellipsis">{{item.title}}</h3>
              <div class="img_box" v-if="item.cover.type===3">
                <van-image class="w33" fit="cover" :src="item.cover.images[0]" />
                <van-image class="w33" fit="cover" :src="item.cover.images[1]" />
                <van-image class="w33" fit="cover" :src="item.cover.images[2]" />
              </div>
              <div class="img_box" v-if="item.cover.type===1">
                <van-image class="w100" fit="cover" :src="item.cover.images[0]" />
              </div>
              <div class="info_box">
                <span>{{item.aut_name}}</span>
                <span>{{item.comm_count}}条评论</span>
                <span>{{item.pubdate | relTime}}</span>
              </div>
            </div>
          </van-cell>
        </van-cell-group>
      </van-list>
  </div>
</template>

<script>
import * as Articles from '@/api/articles'
export default {
  name: 'searchresult',
  data () {
    return {
      loading: false, // 是否开启上拉加载,默认为false
      finished: false, // 是否完成所有数据加载
      articles: [], // 文章列表数组
      page: {
        page: 1, // 当前页数
        per_page: 10 // 每页多少条
      }
    }
  },
  methods: {
    // 方法会在滚动条滚动到底部的时候执行
    async onLoad () {
      // 解构路由参数
      const { q } = this.$route.query // 搜索页面传过来的值
      // 发送请求
      const res = await Articles.searchArticle({ ...this.page, q: q })
      // 将返回的结果追加到articles队尾
      this.articles.push(...res.results)
      // 关闭上拉加载状态
      this.loading = false
      if (res.results.length) {
        // 返回的查询记录有值,表示还有数据未加载完成
        this.page.page++
      } else {
        // 表示全部数据加载完成
        this.finished = true
      }
    }
  }
}
</script>

<style lang="less" scoped>
.container {
  padding-top: 46px;
  height: 100%;
  overflow-y: auto;
  box-sizing: border-box;
}
.article_item {
  h3 {
    font-weight: normal;
    line-height: 2;
  }
  .img_box {
    display: flex;
    justify-content: space-between;
    .w33 {
      width: 33%;
      height: 90px;
    }
    .w100 {
      height: 180px;
      width: 100%;
    }
  }
  .info_box {
    color: #999;
    line-height: 2;
    position: relative;
    span {
      padding-right: 10px;
    }
  }
}
</style>

<template>
  <div>
    <div class="search">
      <input v-model="keyword" class="search-input" type="text" placeholder="输入城市名或拼音">
    </div>
    <div class="search-content" ref="search" v-show="keyword">
      <ul>
        <li class="search-item border-bottom" v-for="item in list" :key="item.id" @click="handleCityClick(item.name)">{{item.name}}</li>
        <li class="search-item border-bottom" v-show="hasNoList">没有找到匹配数据</li>
      </ul>
    </div>
  </div>
</template>

<script>
import Bscroll from 'better-scroll'
import { mapMutations } from 'vuex'
export default {
  name: 'CitySearch',
  props: {
    cities: Object
  },
  data () {
    return {
      keyword: '',
      list: [],
      timer: null
    }
  },
  computed: {
    hasNoList () {
      return !this.list.length
    }
  },
  methods: {
    handleCityClick (city) {
      // 由于此vuex不涉及复杂异步数据，所以可以省略actions(dispatch)，直接调mutations(commit)
      // this.$store.dispatch('changeCity', city)
      // 调mutations(commit)又可替换成vuex提供的高级属性mapMutations
      // this.$store.commit('changeCity', city)
      this.changeCity(city)
      this.$router.push('/')
    },
    ...mapMutations(['changeCity'])
  },
  watch: {
    keyword () {
      if (this.timer) {
        clearTimeout(this.timer)
      }
      if (!this.keyword) {
        this.list = []
        return
      }
      this.timer = setTimeout(() => {
        const result = []
        for (let i in this.cities) {
          this.cities[i].forEach((value) => {
            if (value.spell.indexOf(this.keyword) > -1 || value.name.indexOf(this.keyword) > -1) {
              result.push(value)
            }
          })
        }
        this.list = result
      }, 100)
    }
  },
  mounted () {
    this.scroll = new Bscroll(this.$refs.search);
  }
}
</script>

<style lang="stylus" scoped>
  @import '~@css/varibles.styl'
  .search
    height: .72rem
    background: $bgcolor
    padding: 0 .1rem
    .search-input
      box-sizing: border-box
      height: .62rem
      line-height .62rem
      width: 100%
      color: #666
      padding: 0 .1rem
      text-align: center
      border-radius: .06rem
  .search-content
    overflow: hidden
    position: absolute
    z-index: 1
    top: 1.58rem
    left: 0
    right: 0
    bottom: 0
    .search-item
      line-height .62rem
      padding-left: .2rem
      background: #fff
      color: #666
</style>

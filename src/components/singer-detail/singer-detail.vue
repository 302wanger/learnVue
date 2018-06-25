<template>
  <transition name="slide">
    <music-list :title="title"
                :bg-image="bgImage"
                :songs="songs">
    </music-list>
  </transition>

</template>

<script>
import { mapGetters } from 'vuex'
import { getSingerDetail } from 'api/singer'
import { ERR_OK } from 'api/config'
import { createSong } from 'common/js/song'
import MusicList from 'components/music-list/music-list'

export default {
  data() {
    return {
      songs: []
    }
  },
  computed: {
    title() {
      return this.singer.name
    },
    bgImage() {
      return this.singer.avatar
    },
    ...mapGetters(['singer'])
  },
  created() {
    this._getDetail()
  },
  methods: {
    _getDetail() {
      // 如果在歌手详情页刷新，获取不到歌手id的情况下会跳转到singer首页
      if (!this.singer.id) {
        this.$router.push('/singer')
        return
      }
      getSingerDetail(this.singer.id).then(res => {
        if (res.code === ERR_OK) {
          this.songs = this._normalizeSongs(res.data.list)
        }
      })
    },
    _normalizeSongs(list) {
      let ret = []
      list.forEach(item => {
        let { musicData } = item
        if (musicData.songid && musicData.albummid) {
          ret.push(createSong(musicData))
        }
      })
      return ret
    }
  },
  components: {
    MusicList
  }
}
</script>

<style lang="stylus" scoped>
@import '~common/stylus/variable';

.slider-enter-active, .slider-leave-active {
  transition: all 0.3s;
}

.slider-enter, .slider-leave-to {
  transform: translated3d(100%, 0, 0);
}
</style>

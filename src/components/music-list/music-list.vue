<template>
  <div class="music-list">
    <div class="back" @click="back">
      <i class="icon iconfont icon-fanhui"></i>
    </div>
    <h1 class="title" v-html="title"></h1>
    <div class="bg-image" :style="bgStyle" ref="bgImage">
      <div class="play-container">
        <div class="play" ref="playBtn" v-show="songs.length > 0">
          <i class="icon iconfont icon-play"></i>
          <span class="text">随机播放</span>
        </div>
      </div>
      <div class="filter">
      </div>
    </div>
    <div class="bg-layer" ref="layer"></div>

    <div @scroll="scroll" class="list" ref="list">
      <div class="song-list-container">
        <song-list @select="selectItem" :songs="songs"></song-list>
      </div>
      <div class="loading-container" v-show="!songs.length">
          <loading></loading>          
      </div>
    </div>

  </div>
</template>

<script>
import SongList from "@/base/song-list/song-list";
import loading from "@/base/loading/loading";
import {mapActions, mapMutations} from "vuex"

const RESERVED_HEIGHT = 40;
export default {
  props: {
    bgImage: {
      type: String,
      default: ""
    },
    songs: {
      type: Array,
      default: []
    },
    title: {
      type: String,
      default: ""
    }
  },
  components: {
    SongList,
    loading
  },
  data() {
    return {
      scrollY: 0
    };
  },
  computed: {
    bgStyle() {
      return `background-image:url(${this.bgImage})`;
    }
  },
  mounted() {
    this.imageHeight = this.$refs.bgImage.clientHeight;
    this.minTranslateY = -this.imageHeight + RESERVED_HEIGHT;
    this.$refs.list.style.top = `${this.$refs.bgImage.clientHeight}px`;
  },
  methods: {
    back() {
      this.$router.back();
    },
    scroll(e) {
      this.scrollY = - e.target.scrollTop;
    },
    selectItem(item, index) {
      this.selectPlay({
        list: this.songs,
        index
      })
    },
    ...mapActions([
      'selectPlay'
    ]),
  },
  watch: {
    scrollY(newY) {
      let translateY = Math.max(this.minTranslateY, newY)
      let zIndex = 0
      let scale = 1 //封面图的缩放数值
      let blur = 0
      // this.$refs.layer.style.transform  = `translate3d(0,${translateY}px,0)` //移动layer节点的位置
      // this.$refs.list.style.top = `${this.$refs.bgImage.clientHeight + translateY }px `
      const percent = Math.abs(newY / this.imageHeight) //滚动的距离和封面图的比例
      if(newY > 0) {
        scale = 1 + percent
        zIndex = 10
      } else {
        blur = Math.min(20 * percent, 20)
      }
      // this.$refs.filter.style.backdrop = `blur(${blur}px)`
      // if(newY < this.minTranslateY) {
      //   zIndex = 10
      //   this.$refs.bgImage.style.paddingTop = 0
      //   this.$refs.bgImage.style.height = `${RESERVED_HEIGHT}px`
      //   this.$refs.playBtn.style.display = 'none'
      // } else {
      //   this.$refs.bgImage.style.paddingTop = '70%'
      //   this.$refs.bgImage.style.height = 0
      //   this.$refs.playBtn.style.display = 'block'
      // }
      // this.$refs.bgImage.style.zIndex = zIndex
      // this.$refs.bgImage.style.transform = `scale(${scale})`
    }
  }
};
</script>

<style lang="scss" scoped>
.music-list {
  position: fixed;
  z-index: 100;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  background: #ffffff;

  .back {
    position: absolute;
    top: 0;
    left: 6px;
    z-index: 50;

    .icon-fanhui {
      display: block;
      padding: 10px;
      font-size: 22px;
      color: $theme-color;
      font-weight: bolder;
    }
  }

  .title {
    position: absolute;
    top: 0;
    left: 10%;
    z-index: 40;
    width: 80%;
    /* no-wrap(); */
    text-align: center;
    line-height: 40px;
    font-size: 18px;
    color: #fff;
  }

  .bg-image {
    position: relative;
    width: 100%;
    height: 0;
    padding-top: 70%;
    transform-origin: top;
    background-size: cover;

    .play-container {
      position: absolute;
      bottom: 20px;
      z-index: 50;
      width: 100%;

      .play {
        box-sizing: border-box;
        width: 135px;
        padding: 7px 0;
        margin: 0 auto;
        text-align: center;
        border: 1px solid $theme-color;
        color: $theme-color;
        border-radius: 100px;
        font-size: 0;

        .icon-play {
          display: inline-block;
          vertical-align: middle;
          margin-right: 6px;
          font-size: 16px;
        }

        .text {
          display: inline-block;
          vertical-align: middle;
          font-size: 12px;
        }
      }
    }

    .filter {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(7, 17, 27, 0.4);
    }
  }

  .bg-layer {
    position: relative;
    height: 100%;
    background: #fff;
  }

  .list {
    position: fixed;
    top: 0;
    bottom: 0;
    width: 100%;
    background: #ffffff;
    // overflow: hidden;
    overflow-y: scroll;
    .song-list-container {
      padding: 20px 30px;
    }
  }
}
</style>

<template>
  <div id="app" :class="{'fix': isFix}">
    <v-header :seller="seller"></v-header>
    <div id="tab" class="border-1px">
      <router-link to="/goods">商品</router-link>
      <router-link to="/rating">评论</router-link>
      <router-link to="/seller">商家</router-link>
    </div>
    <router-view></router-view>
  </div>
</template>

<script>
import VHeader from './views/VHeader.vue';
import axios from 'axios';
import _ from 'lodash';

export default {
  name: 'app',
  components: {
    VHeader
  },
  data() {
    return {
      seller: {},
      isFix: false,
      headerHeight: 0,
      scrollTop: 0,
      // scrollHeight: 0,
      // switchBarHeight: 0,
      throttleScroll: null
    };
  },
  /* computed: {
    contentMinHeight() {
      const windowHeight = document.documentElement.clientHeight;
      return this.isFix ? windowHeight - this.switchBarHeight : windowHeight - this.headerHeight - this.switchBarHeight;
    },
    contentMarginTop() {
      return this.isFix ? this.switchBarHeight : 0;
    }
  }, */
  created() {
    axios.get('/api/seller').then((response) => {
      // console.log(response);
      response = response.data;
      if (response.errno === 0) {
        this.seller = response.data;
      }
    });
  },
  methods: {
    handleScroll() {
      this.setData();
      // console.log('scrolling');

      // 判断是否吸顶效果
      // console.log(this.scrollTop);
      if (!this.isFix) {
        if (this.scrollTop >= this.headerHeight) {
          this.isFix = true;
        } else {
          this.isFix = false;
        }
      }
    },
    setData() {
      this.headerHeight = this.$el.querySelector('.header').clientHeight;
      // this.switchBarHeight = this.$el.querySelector('.goods').clientHeight;
      this.scrollTop = document.documentElement.scrollTop || window.pageYOffset || document.body.scrollTop;
      // this.scrollHeight = document.documentElement.scrollHeight || document.body.scrollHeight;
    }
  },
  mounted() {
    this.$nextTick(() => {
      // 节流监听滚动事件
      let that = this;
      window.addEventListener('scroll', this.throttleScroll, false);
      // if (this.isFix) {
      window.addEventListener('click', function() {
        console.log('click');
        that.isFix = false;
        console.log(that.isFix);
      });
      // }
    });
    this.throttleScroll = _.throttle(this.handleScroll, 100);
  },
  destoryed() {
    window.removeEventListener('scroll', this.throttleScroll);
  }
};

</script>

<style lang="scss">
  @import './design/index.scss';

  #app {
    &.fix {
      .goods {
        position: fixed;
        top: 0;
        bottom: 0;
      }
    }
    #tab {
      @include border-1px( rgba(7, 17, 27, 0.1));
      position: relative;
      display: flex;
      width: 100%;
      height: 40px;
      line-height: 40px;
      background-color: #fff;
      a {
        flex: 1;
        text-align: center;
        display: block;
        font-size: 14px;
        color: rgb(77, 85, 93);
        &.router-link-active {
          font-size: 14px;
          color: rgb(240, 20, 20);
        }
      }
    }
  }
</style>

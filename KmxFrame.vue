<template>
  <div class="container">
    <iframe
      class="frame"
      :src="src"
      frameborder="0"
      :style="{height: calculatedHeight, marginTop: calculatedMarginTop}"
      v-if="kmxConnected"
      @load="iframeReady = true;"></iframe>
    <div class="no-frame" v-else>
      <div class="kmx-logo-wrapper">
        <i class="kmx-logo"></i>
      </div>
      <h2>不能成功连接到KMX。<a @click="hurry">重试</a></h2>
    </div>

    <Spin
      fix
      size="large"
      v-if="!iframeReady"></Spin>
  </div>
</template>

<script>

import { Spin } from 'iview'
import config from 'src/config'
import api from './api'

export default {
  name: 'KmxFrame',
  components: {
    Spin
  },
  props: {
    targetUrl: String,
    cutOffBreadCrumb: {
      type: [Boolean, Number],
      default: false
    }
  },
  data () {
    return {
      src: '',
      oldSrc: '',
      token: '',
      iframeReady: false,
      kmxConnected: true,
      timeout: '',
      casLogin: config.casLoginUrl
    }
  },
  computed: {
    calculatedHeight () {
      if (this.cutOffBreadCrumb === true) {
        return 'calc( 100% + 115px )'
      } else if (this.cutOffBreadCrumb === false) {
        return 'calc( 100% + 73px )'
      } else { // number in px
        return `calc( 100% + ${73 + this.cutOffBreadCrumb}px )`
      }
    },
    calculatedMarginTop () {
      if (this.cutOffBreadCrumb === true) {
        return '-105px'
      } else if (this.cutOffBreadCrumb === false) {
        return '-63px'
      } else { // number in px
        return `-${63 + this.cutOffBreadCrumb}px`
      }
    }
  },
  mounted () {
    // note if you manully initialize data in mount(), you should be aware of the order!
    this.timeout = config.iframeTimeoutInMillis
    this.token = api.getK2Key() || throw new Error('请在api/index.js中设置获取K2Key的getter')
    if (!this.token) {
      console.error(`iframe试图访问${this.targetUrl}页面，但是token是空的！这是不合理的情况。请联系开发人员解决此问题。`)
    }

    this.src = this.casLogin + '?token=' + this.token + '&service=' + this.targetUrl
    this.hurry()
  },
  methods: {
    hurry () {
      if (process.env.NODE_ENV === 'development') {
        console.log(`iframe正在加载${this.targetUrl}页面， CAS位于${this.casLogin}`)
      }
      // what a cute name
      this.iframeReady = false
      this.kmxConnected = true
      this.src = this.oldSrc || this.src
      setTimeout(() => {
        if (!this.iframeReady) {
          // page not ready even after x ms. x defined in prod.env.js

          // hide spin
          this.iframeReady = true
          this.oldSrc = this.src
          this.src = 'about:blank'
          this.kmxConnected = false
        }
      }, this.timeout)
    }
  }
}
</script>

<style scoped>

  .container {
    position: relative;
    overflow: hidden;
  }

  .frame {
    width: calc(100% + 210px);
    /*height: calc( 100% + 73px );*/
    margin-left: -210px;
    /*margin-top: -63px;*/
    overflow: hidden;
  }

  .no-frame {
    width: 100%;
    height: 100%;
    text-align: center;
    color: #999999;
  }

  .kmx-logo-wrapper {
    display: block;
    position: relative;
    box-sizing: content-box;
    max-width: 400px;
    width: 40%;
    height: 0;
    margin: 150px auto auto auto;
    padding-bottom: 25%;
  }

  .kmx-logo {
    position: absolute;
    width: 100%;
    height: 100%;
    left: 0;
    top: 0;
    background: no-repeat center / contain url("./kmx-mono.png");
  }
</style>

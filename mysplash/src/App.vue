<template>
  <div id="app">

    <div class="loading">
      <img src="./assets/circles.svg" alt="" v-if="loading"/>
      <span v-if="netFailed">網絡出錯了，請檢查網絡</span>
    </div>
    <waterfall :line-gap="gaps" :watch="items" v-if="!loading">
  <!-- each component is wrapped by a waterfall slot -->
  <waterfall-slot
    v-for="(item, index) in items"
    :width="item.width"
    :height="item.height"
    :order="index"
    :key="item.id"
    style="padding:2px;"
  >
  <!-- <div v-lazy-container="{ selector: 'img'}">
  <img :data-src="item.urls.small" data-loading="./assets/logo.png" style="width:100%;height:100%;"> 
</div> -->
    <img v-lazy="item.urls.small" :alt="item.description" style="width:100%;height:100%;display:block;"/>
  </waterfall-slot>
</waterfall>
  </div>
</template>

<script>
import Vue from 'vue';
import Waterfall from "vue-waterfall/lib/waterfall";
import WaterfallSlot from "vue-waterfall/lib/waterfall-slot";

export default {
  name: "App",
  data() {
    return {
      baseUrl: "https://unsplash.com/napi/collections/featured",
      items: [],
      page: 1,
      per_page: 8,
      loading : true,
      netFailed : false,
      gaps : 500
    };
  },
  mounted() {
    this.fetchData();
    var that = this;
    window.addEventListener("scroll", function() {
      var wScrollY = window.scrollY; // 当前滚动条位置  
      var wInnerH = window.innerHeight; // 设备窗口的高度（不会变）  
      var bScrollH = document.body.scrollHeight; // 滚动条总高度      
      if (wScrollY + wInnerH >= bScrollH) { 
          console.log(wScrollY , wInnerH , bScrollH);
          that.itemAdds();
      }else {
        console.log('err');
      }
    });
  },
  components: {
    Waterfall,
    WaterfallSlot
  },
  methods: {
    fetchData() {
      this.$ajax(this.baseUrl, {
        params: {
          page: this.page++,
          per_page: this.per_page
        }
      })
        .then(res => {
          this.loading = false;
          let len = res.data.length;
          for (let i = 0; i < len; i++) {
            this.items.push(res.data[i].cover_photo);
          }
          console.log(this.items);
        })
        .catch(err => {
          this.netFailed = true;
          this.loading = false;
          console.log(err);
        });
    },
    itemAdds() {
      this.fetchData();
    }
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  position: relative;
  width: 100%;
  height: 300px;
}
body {
  margin: 5px;
  font-family: Helvetica, sans-serif;
}
.item {
  position: absolute;
  top: 5px;
  left: 5px;
  right: 5px;
  bottom: 5px;
  font-size: 1.2em;
  color: rgb(0, 158, 107);
}
.item:after {
  content: attr(index);
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  -webkit-transform: translate(-50%, -50%);
  -ms-transform: translate(-50%, -50%);
}
.wf-transition {
  transition: opacity 0.3s ease;
  -webkit-transition: opacity 0.3s ease;
}
.wf-enter {
  opacity: 0;
}
.vue-waterfall-slot {
  padding: 2px;
}
.loading {
  width: 100px;
  height: 100px;
  position: absolute;
  top: 50%;
  left: 50%;
  margin-left: -50px;
  margin-top: -50px;
  /* transition: all .2s ease; */
}
.loading > img {
  width: 100%;
  height: 100%;
  display: block;
}
</style>

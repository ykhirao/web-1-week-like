<template>
  <div id="app">
    <v-form ref="form" lazy-validation @submit.prevent="search">
      <v-text-field
        v-model="name"
        label="Write Your likes!!!"
        required
      ></v-text-field>
    </v-form>
    <VueTinder ref="tinder" :queue.sync="queue" @submit="submit">
      <template slot-scope="{ data }">
        <div class="pic" :style="`background-image:url(${data.key})`"></div>
      </template>
      <img
        class="like-pointer"
        slot="like"
        src="https://johnnydan.oss-cn-beijing.aliyuncs.com/vue-tinder/like-txt.png"
      />
      <img
        class="nope-pointer"
        slot="nope"
        src="https://johnnydan.oss-cn-beijing.aliyuncs.com/vue-tinder/nope-txt.png"
      />
      <img
        class="super-pointer"
        slot="super"
        src="https://johnnydan.oss-cn-beijing.aliyuncs.com/vue-tinder/super-txt.png"
      />
    </VueTinder>
    <div class="btns">
      <img
        src="https://johnnydan.oss-cn-beijing.aliyuncs.com/vue-tinder/nope.png"
        @click="decide('nope')"
      />
      <img
        src="https://johnnydan.oss-cn-beijing.aliyuncs.com/vue-tinder/super-like.png"
        @click="decide('super')"
      />
      <img
        src="https://johnnydan.oss-cn-beijing.aliyuncs.com/vue-tinder/like.png"
        @click="decide('like')"
      />
    </div>
  </div>
</template>

<script>
import VueTinder from "vue-tinder";
import axios from "axios";

export default {
  name: "App",
  components: {
    VueTinder,
  },
  data: function() {
    return {
      name: "",
      queue: [],
      page: 1,
    };
  },
  created() {
    this.queue = [
      {
        key:
          "https://pixabay.com/get/53e5d4464f56b108f5d084609629317b1c3cdbe4514c704c7d267cd0924dc250_1280.jpg",
      },
      {
        key:
          "https://pixabay.com/get/57e1d1444250b108f5d084609629317b1c3cdbe4514c704c7d267cd0924dc250_1280.jpg",
      },
      {
        key:
          "https://pixabay.com/get/53e0dc4b4351ac14f6da8c7dda7936781036dde052536c48702772d39348c55fb0_1280.jpg",
      },
      {
        key:
          "https://pixabay.com/get/57e6d0424356a914f6da8c7dda7936781036dde052536c48702772d39348c55fb0_1280.jpg",
      },
      {
        key:
          "https://pixabay.com/get/57e7d6404d4fad0bffd8992cc62e3277163adfe74e5074417c287fd1974acd_1280.jpg",
      },
    ];
  },
  methods: {
    /**
     * @method getData
     */
    getData() {
      const params = {
        key: process.env.VUE_APP_KEY,
        q: this.name,
        lang: "ja",
        per_page: 20,
        page: this.page,
        orientation: "vertical",
      };
      axios
        .get("https://pixabay.com/api/", { params })
        .then((res) => {
          const list = res.data.hits;
          if (!list || list.length === 0) return;
          this.queue = this.queue.concat(
            list.map((v) => {
              return { key: v.largeImageURL };
            })
          );
        })
        .catch((error) => {
          alert("error");
          console.log(error, params);
        });
    },

    /**
     * @method search
     */
    search() {
      console.log("fire");
      this.queue = [];
      this.getData();
    },
    /**
     *
     * @method submit
     * @param  {String} choice
     */
    decide(choice) {
      this.$refs.tinder.decide(choice);
    },
    /**
     *
     * @method submit
     * @param  {Object} choice {type,key}
     */
    submit(choice) {
      switch (choice) {
        case "nope": // 左滑
          break;
        case "like": // 右滑
          break;
        case "super": // 上滑
          break;
      }

      if (this.queue.length < 2) {
        this.page += 1;
        this.getData();
      }
    },
  },
};
</script>

<style>
/* 此样式可能仅适用本demo，仅供参考，可根据具体设计稿或喜好自定义 */
html,
body {
  height: 100%;
}

body {
  /* background: #20262e; */
  overflow: hidden;
}

/* 直接script引用建议增加，不然会引发闪烁，单文件组件不需要 */
[v-cloak] {
  display: none;
}

/* 注意！组件的宽高必须设置，不然出不来！！！ */
#app .vue-tinder {
  position: absolute;
  z-index: 1;
  left: 0;
  right: 0;
  top: 70px;
  margin: auto;
  width: calc(100% - 20px);
  height: calc(100% - 70px - 18%);
  min-width: 300px;
  max-width: 355px;
}

/* 卡片内的3种状态指示器位置，透明度会由组件自动调整 */
.nope-pointer {
  right: 10px;
}
.like-pointer {
  left: 10px;
}
.nope-pointer,
.like-pointer {
  position: absolute;
  z-index: 1;
  top: 20px;
  width: 64px;
  height: 64px;
}
.super-pointer {
  position: absolute;
  z-index: 1;
  bottom: 80px;
  left: 0;
  right: 0;
  margin: auto;
  width: 112px;
  height: 78px;
}

/* slot内图片样式 */
.pic {
  width: 100%;
  height: 100%;
  background-size: cover;
}

/* 按钮样式 */
.btns {
  position: absolute;
  left: 0;
  right: 0;
  bottom: 0;
  margin: auto;
  height: 18%;
  display: flex;
  align-items: center;
  justify-content: center;
  min-width: 300px;
  max-width: 355px;
}
.btns img {
  width: 80px;
}
</style>

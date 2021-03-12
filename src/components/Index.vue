/* eslint-disable consistent-return */
<template>
  <div class="BigGo">
    <div class="main">
      <h1
        class="text-hide"
        style="background-image: url(https://biggo.com.tw/images/biggo/header_biggo_logov2@2x.png?v=12);width:200px;height:200px;"
      >
        BigGo
      </h1>
      <div class="search-body">
        <div class="search-groups">
          <input type="text" class="search-Input" v-model="keyword" />
          <button type="button" class="search-btn">
            <i class="fas fa-search"></i>
          </button>
          <div class="clear-btn" v-show="keyword" @click="ClearSearch">
            <i class="fas fa-times"></i>
          </div>
        </div>
        <p class="mt-4 serach-show">
          <span v-show="keyword">包含「 {{ keyword }} 」的關鍵字</span>共有
          {{ newShopArray.length }} 間商店
        </p>
        <div class="suggestions">
          <ul class="suggestions-groups p-0" v-if="keyword">
            <li
              class="border-bottom suggestions-item"
              v-for="(renderShop, index) in newShopArray"
              :key="index"
              @click="ClickShopName(renderShop.item.name)"
            >
              <i class="fas fa-search mr-2"></i>
              {{ renderShop.item.name }}
            </li>
          </ul>
        </div>
      </div>
    </div>
    <hr />
    <div class="container mt-4">
      <h3 v-if="newShopArray.length === 0">
        沒有搜到相關商店，請再次搜尋d(`･∀･)b
      </h3>
      <a
        href=""
        class="view-link mr-4 mb-4"
        v-for="(name, index) in newShopArray"
        :key="index"
      >
        <div class="view-item">
          <div class="view-img">
            <img v-lazy="name.detail.image" alt="" />
          </div>
          <h5 class="mt-3">{{ name.item.name }}</h5>
          <p>獲得回饋金0.88%</p>
        </div>
      </a>
    </div>
    <p></p>
  </div>
</template>

<script>
import _ from 'lodash';
import json from '../../json/db-1615452601352.json';

export default {
  name: 'HelloWorld',
  data() {
    return {
      myJson: json,
      keyword: '',
      shopArray: [],
      newShopArray: null,
    };
  },
  watch: {
    keyword() {
      this.DebouncedGetAnswer();
    },
  },
  methods: {
    GetShopData() {
      Object.values(this.myJson).forEach((item) => {
        this.shopArray.push(item);
      });
      this.newShopArray = JSON.parse(JSON.stringify(this.shopArray));
    },
    ClickShopName(index) {
      this.shopArray.forEach((i) => {
        if (i.item.name === index) {
          this.keyword = i.item.name;
        }
      });
    },
    ClearSearch() {
      this.keyword = '';
    },
    FilterKeyword() {
      if (this.keyword) {
        const searchArray = [];
        // eslint-disable-next-line consistent-return
        Object.keys(this.myJson).forEach((key) => {
          const storeId = key;
          const storeName = this.myJson[key].item.name.toLowerCase();
          // 關鍵字搜索商店id
          if (storeId.includes(this.keyword)) {
            searchArray.push(this.myJson[storeId]);
            // 關鍵字搜索商店name
          } else if (storeName.includes(this.keyword.toLowerCase())) {
            searchArray.push(this.myJson[storeId]);
          }
        });
        this.newShopArray = searchArray;
      } else {
        this.newShopArray = this.shopArray;
      }
    },
  },
  created() {
    this.GetShopData();
    this.DebouncedGetAnswer = _.debounce(this.FilterKeyword, 200); // 使用debounce提升效能
  },
};
</script>

<style scoped lang="scss">
.BigGo {
  max-width: 100%;
  margin: 0 auto;
}
.text-hide {
  background-position: center;
  background-repeat: no-repeat;
}
.main {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.search-body {
  width: 300px;
  .serach-show {
    font-size: 18px;
    font-weight: bold;
  }
}
.search-groups {
  position: relative;
  width: 100%;
}
.search-Input {
  width: 80%;
  // height: 40px;
  padding: 6px;
  border: 2px solid #cdc3b7;
  border-radius: 5px 0 0 5px;
  border-right: 0;
  outline: none;
  background-color: rgba(218, 213, 213, 0.5);
}
.search-btn {
  background-color: #fff;
  border: 2px solid #cdc3b7;
  border-radius: 0 5px 5px 0;
  transform: translateX(-2px);
  height: 40px;
  width: 40px;
  i {
    color: #7d7265;
  }
  &:hover {
    background-color: #d6997a;
    opacity: 0.7;
    transition: all 0.3s;
    i {
      color: white;
      transition: all 0.3s;
    }
  }
}
.clear-btn {
  transform: translate(75px, -30px);
  cursor: pointer;
}
.suggestions-groups {
  list-style: none;
  margin-top: 5px;
  .suggestions-item {
    letter-spacing: 1px;
    text-align: left;
    padding: 3px;
    border-radius: 5px;
    cursor: pointer;
    &:hover {
      transition: all 0.5s;
      background-color: rgba(255, 193, 7, 0.3);
    }
  }
}
.container {
  max-width: 1200px;
  height: auto;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}
.view-item {
  width: 250px;
  .view-img {
    // width: 33%;
    padding: 10px;
    height: 100%;
    margin: 0 auto;
    img {
      width: 100%;
      height: 100%;
    }
  }
  p {
    color: #ff5f01;
  }
}
.view-link {
  text-decoration: none;
  background-color: #fff;
  color: black;
  // border: 1px solid ghostwhite;
  box-shadow: 0 3px 3px gainsboro;
  &:hover {
    transition: all 0.5s;
    transform: scale(1.04);
  }
}
</style>

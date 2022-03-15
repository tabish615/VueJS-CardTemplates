<template>
  <div style="width: 80%; margin: auto">
    <b-input-group class="mb-2">
      <b-input-group-prepend is-text>
        <b-icon icon="search" style="width: 17px;"></b-icon>
      </b-input-group-prepend>
      <b-form-input
        type="search"
        placeholder="Search"
        v-model="searchValue"
        style="border-left:0; box-shadow:none; border-radius:0; border-color:#ced4da; height:56px"
      >
      </b-form-input>
    </b-input-group>
    <div class="masonry row">
      <div
        v-for="(post, key) in resultQuery"
        :key="key"
        class="card col"
      >
        <div class="card-content">
          <div v-if="post.image !== null">
            <img :src="post.image" alt="Image" class="img-responsive" @load="rendered">
          </div>
          <div class="p-20">
            <div style="display: flex; justify-content: space-between">
              <p class="small text-muted uppercase">{{ post.category }}</p>
              <p class="small text-muted">12 Days Ago</p>
            </div>
            <p class="card-title">{{ post.title }}</p>
            <p class="card-description">{{ post.description }}</p>
            <div style="display: flex; justify-content: space-between; align-items: center">
              <div style="width: 50%">
                <b-avatar src="https://placekitten.com/300/300"></b-avatar>
                <a href="#" class="card-link" style="margin-left: 5%">Glen Williams</a>
              </div>
              <div>
                <a href="#" class="card-link">Read More</a>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Vue from 'vue';
import axios from 'axios';
import VueAxios from 'vue-axios';

Vue.use(VueAxios, axios);

export default {
  name: 'CardTemplate',
  data() {
    return {
      list: [],
      searchValue: '',
      imagesCount: 0,
      imageCounter: 0,
    };
  },
  mounted() {
    this.getPosts();
  },
  computed: {
    resultQuery() {
      if (this.searchValue) {
        this.calculateImageCount();
        return this.list.filter((item) => item.title.toLowerCase()
          .includes(this.searchValue.toLowerCase()));
      }
      return this.list;
    },
  },

  watch: {
    imagesCount() {
      if (this.imagesCount === this.imageCounter
          || this.searchValue.length
          || this.imagesCount > this.imageCounter) {
        this.resizeAllMasonryItems();
      }
    },
  },

  methods: {
    rendered() {
      this.imagesCount++;
    },

    getPosts() {
      Vue.axios
        .get('http://api.mediastack.com/v1/news?access_key=2eec0ee2c6f645a62ddf7f62251de244')
        .then((resp) => {
          this.list = resp.data.data;
          this.calculateImageCount();
        });
    },

    calculateImageCount() {
      this.imageCounter = 0;
      for (let i = 0; i < this.list.length; i++) {
        if (this.list[i].image !== null) {
          this.imageCounter++;
        }
      }
    },

    resizeMasonryItem(item) {
      const grid = document.getElementsByClassName('masonry')[0];
      const rowGap = parseInt(window.getComputedStyle(grid).getPropertyValue('grid-row-gap'), 10);
      const rowHeight = parseInt(window.getComputedStyle(grid).getPropertyValue('grid-auto-rows'), 10);
      const rowSpan = Math.ceil((item.querySelector('.card-content').getBoundingClientRect().height + rowGap) / (rowHeight + rowGap));
      item.style.gridRowEnd = `span ${rowSpan}`;
    },

    resizeAllMasonryItems() {
      const allItems = document.getElementsByClassName('card');
      for (let i = 0; i < allItems.length; i++) {
        this.resizeMasonryItem(allItems[i]);
      }
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">

.mb-2 {
  margin: 50px auto !important;
}
.p-20 {
  padding: 20px;
}
.row > * {
  padding: 0;
}
.card {
  border-radius: 16px !important;
  position: relative;
  display: flex;
  flex-direction: column;
  min-width: 0;
  word-wrap: break-word;
  background-color: #fff;
  background-clip: border-box;
  filter: drop-shadow(0px 24px 64px rgba(0,0,0,0.06));
  transition: all 0.3s ease;
}
.card-description {
  font-size: 13px;
  line-height: 19px;
  font-weight: 400;
  text-overflow: ellipsis;
  overflow: hidden;
  display: -webkit-box;
  -webkit-line-clamp: 7;
  -webkit-box-orient: vertical;
}
.card-title {
  font-family: 'DM Serif Display', serif;
  font-weight: 400;
  font-size: 20px;
  line-height: 25px;
  letter-spacing: -0.3px;
}
.input-group-text {
  background-color: white;
  border-radius: 0px;
  height: 56px;
}
.img-responsive {
  width: 100%;
  border-top-left-radius: 16px;
  border-top-right-radius: 16px;
}
.masonry {
  display: grid;
  grid-gap: 15px;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  grid-auto-rows: 0;
}
.card-link {
  font-size: 11px;
  text-decoration: none;
}
.card-content p.small {
  letter-spacing: 0px;
  font-family: 'Open Sans', sans-serif;
  font-weight: 600;
}
.card:hover {
  filter: drop-shadow(0px 24px 40px rgba(0,0,0,0.16));
}
.uppercase {
  text-transform: uppercase;
}
</style>

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
    <p v-if='isLoading'>Loading...</p>
    <div class="masonry" v-else>
      <div
        v-for="(post, key) in resultQuery"
        :key="key"
        class="card"
      >
        <div class="card-content">
          <div v-if="post.image !== null">
            <img :src="post.image" alt="Image" class="img-responsive" @load="rendered">
          </div>
          <div class="p-20">
            <p class="small text-muted">{{ post.category }}</p>
            <p class="card-title">{{ post.title }}</p>
            <p class="card-description">{{ post.description }}</p>
            <b-avatar></b-avatar>
            <a href="#" class="card-link">{{ post.author }}</a>
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
      searchValue: null,
      imagesCount: 0,
      imageCounter: 0,
      isLoading: false,
    };
  },
  mounted() {
    this.getPosts();
  },
  computed: {
    resultQuery() {
      if (this.searchValue) {
        return this.list.filter((item) => this.searchValue
          .toLowerCase()
          .split(' ')
          .every((v) => item.title.toLowerCase().includes(v)));
      }
      return this.list;
    },
  },
  created() {
    const masonryEvents = ['load', 'resize'];
    const vm = this;
    masonryEvents.forEach((event) => {
      window.addEventListener(event, vm.resizeAllMasonryItems);
    });
  },
  watch: {
    imagesCount() {
      if (this.imagesCount === this.imageCounter) {
        this.resizeAllMasonryItems();
        this.isLoading = false;
      }
    },
  },

  methods: {
    rendered() {
      this.imagesCount++;
      this.resizeAllMasonryItems();
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
.card {
  border-radius: 15px !important;
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
  line-height: 25px;
  font-weight: normal;
  font-size: 20px;
  font-weight: 500;
}
.input-group-text {
  background-color: white;
  border-radius: 0px;
  height: 56px;
}
.img-responsive {
  width: 100%;
  border-top-left-radius: 15px;
  border-top-right-radius: 15px;
}
.masonry {
  display: grid;
  grid-gap: 15px;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  grid-auto-rows: 0;
}
.card-link {
  font-size: 11px;
  text-decoration: none;
}
</style>

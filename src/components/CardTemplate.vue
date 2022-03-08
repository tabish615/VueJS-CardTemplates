<template>
  <div style="width: 80%; margin: auto">
    <b-input-group class="mb-2">
      <b-input-group-prepend is-text>
        <b-icon icon="search" font-scale="1.5"></b-icon>
      </b-input-group-prepend>
      <b-form-input
        type="search"
        placeholder="Search"
        v-model="searchValue"
        style="border-left: 0; box-shadow: none"
      >
      </b-form-input>
    </b-input-group>
    <b-card-group class="template-view">
      <b-card
        v-for="item in resultQuery"
        v-bind:key="item.title"
        :img-src="item.image"
        img-alt="Image"
        img-top
        class="card-view"
      >
        <b-card-text class="small text-muted">
          {{ item.category }}
        </b-card-text>
        <b-card-title>
          {{ item.title }}
        </b-card-title>
        <b-card-text>
          {{ item.description }}
        </b-card-text>
        <b-avatar></b-avatar>

        <b-link href="#" class="card-link">{{ item.author }}</b-link>
      </b-card>
    </b-card-group>
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
    };
  },
  mounted() {
    Vue.axios
      .get('http://api.mediastack.com/v1/news?access_key=47bfd4b62d96adcf18aa83f29cd9a238')
      .then((resp) => {
        this.list = resp.data.data;
      });
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
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.mb-2 {
  margin: 30px auto !important;
}
.template-view {
  display: flex;
  flex-direction: column;
  max-height: 523vh;
  align-content: space-between;
  flex-wrap: wrap;
}
.card-img-top {
  border-top-left-radius: 15px !important;
  border-top-right-radius: 15px !important;
}
.card-view {
  margin-bottom: 20px !important;
  max-height: 900px;
  width: 300px;
  border-radius: 15px !important;
  border: 1px solid rgba(0, 0, 0, 0.125) !important;
}

@media (max-width: 1140px) {
  .template-view {
    max-height: 600vh;
  }
}

@media (max-width: 925px) {
  .template-view {
    max-height: 845vh;
  }
}
@media (max-width: 600px) {
  .template-view {
    max-height: 2165vh;
  }
}
</style>

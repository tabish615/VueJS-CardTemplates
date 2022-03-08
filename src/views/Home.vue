<template>
  <div>
    <!-- <img alt="Vue logo" src="../assets/logo.png"> -->
    <CardTemplate msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
// @ is an alias to /src
import CardTemplate from '@/components/CardTemplate.vue';

export default {
  name: 'Home',
  components: {
    CardTemplate,
  },
};
</script>

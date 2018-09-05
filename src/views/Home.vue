<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <img alt="Git logo" src="../assets/github.png">
    <Dashboard msg="Page to list issues from Github repo, using Vue.js."/>
  </div>
</template>

<script>
// @ is an alias to /src
import Dashboard from '@/components/Dashboard.vue';

export default {
  name: 'Home',
  components: {
    Dashboard,
  },
};
</script>

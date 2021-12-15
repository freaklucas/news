<template>
  <Layout>
    <h2 class="mb-8 text-4xl font-semibold text-center capitalize">
      News Section: <span class="text-gray-700">{{ section }}</span>
    </h2>
    <NewsFilter v-model="section" />
    <NewsList :posts="posts" />
  </Layout>
</template>

<script>
import Layout from "./components/Layout.vue";
import NewsFilter from "./components/NewsFilter.vue";
import NewsList from "./components/NewsList.vue";

import data from "./posts.json";

export default {
  components: {
    Layout,
    NewsFilter,
    NewsList,
  },
  data() {
    return {
      section: "home",
      posts: data.posts,
    };
  },
};
</script>
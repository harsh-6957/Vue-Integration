<template lang="pug">
  .container.mx-auto
    .clearfix
      BaseHeading(:level="2") Danh sách bài viết
      PostListView(:posts="getPosts")
</template>

<script>
import PostListView from "@/components/PostListView";
import { mapGetters } from "vuex";
export default {
  name: "Home",
  components: {
    PostListView,
  },
  async created() {
    if (!this.getPosts) await this.$store.dispatch("fetchPostsAll");
  },
  computed: {
    ...mapGetters(["getPosts"]),
  },
};
</script>

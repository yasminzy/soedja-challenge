<template>
  <div class="posts-container wrapper">
    <article class="wrapper">
      <ul class="content mh-100">
        <PostListItem
          v-for="item in content"
          :key="item.id"
          :date="item.webPublicationDate"
          :identifier="item.id"
          :img="item.fields.thumbnail"
          :tags="item.tags"
          :title="item.webTitle"
          :trail-text="item.fields.trailText"
        />
      </ul>

      <SearchBox />
    </article>

    <aside data-aos="fade">
      <ul class="tags wrapper">
        <li v-for="item in tags" :key="item.id" class="tags-li">
          <nuxt-link
            :to="{ name: 'tag', params: { tag: item.id } }"
            class="tags-a"
          >
            <small>#{{ item.webTitle }}</small>
          </nuxt-link>
        </li>
      </ul>
    </aside>
  </div>
</template>

<script>
export default {
  computed: {
    content() {
      return this.$store.getters.content;
    },
    tags() {
      return this.$store.getters.tags;
    }
  },
  async created() {
    // Get articles related to the selected section
    let route = this.$store.state.route.path.replace("/", "");

    // If the route does not have a % symbol,
    if (!route.includes("%")) {
      // get the content for this section
      await this.$store.dispatch("getSectionContent", route);
    } else {
      // If the route has that symbol, but it is not a link to an article, fix the route
      route = route.replace(/%252F/g, "/");

      // then go to the _tag page
      this.$router.push({
        name: "tag",
        params: { tag: route }
      });
    }
  }
};
</script>

<style lang="postcss" scoped>
.content {
  @media (--sm) {
    column-gap: var(--space);
    grid-template-columns: 1fr 1fr;
  }

  @media (--md) {
    grid-template-columns: 1fr;
  }

  & > .li {
    box-shadow: var(--shadow);
    padding: var(--space);
    width: 100%;
  }
}

.tags {
  list-style-type: none;
  padding-left: 0;
  text-align: center;

  & .tags-li:not(:last-child) {
    margin-bottom: var(--space);
  }

  & .tags-a {
    color: var(--dark);
    display: block;
  }

  @media (--sm) {
    display: grid;
    grid-template-columns: 1fr 1fr;
  }

  @media (--md) {
    grid-template-columns: 1fr;
    position: fixed;
    text-align: left;
  }
}
</style>

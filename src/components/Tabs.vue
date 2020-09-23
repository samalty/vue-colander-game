<template>
  <article>
    <header class="tabs">
      <ul>
        <li v-for="(tab, index) in tabs" :key="index">
          <div :class="{ 'is-active': tab.isActive }"
               v-on:click="selectTab(tab)">
            {{ tab.name }}
          </div>
        </li>
      </ul>
    </header>
      <section class="tabs-details">
        <slot></slot>
      </section>
    </article>
</template>

<script>
export default {
  data() {
    return {
      tabs: []
    }
  },
  methods: {
    selectTab(selectedTab) {
      // Update selected tab's isActive status
      this.tabs.forEach(tab => {
        tab.isActive = tab.name === selectedTab.name;
      });
    }
  },
  created() {
    // Fetch data from child components
    this.tabs = this.$children;
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
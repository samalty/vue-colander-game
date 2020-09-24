<template>
  <article>
    <header class="tab">
      <ul>
        <li v-for="(tab, index) in tabs" :key="index">
          <div :class="{ 'is-active': tab.isActive }"
               v-on:click="selectTab(tab)">
            {{ tab.name }}
          </div>
        </li>
      </ul>
    </header>
      <section class="tab-detail">
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

  .tab{
    color: white;
    border-bottom: 1px solid #ffffff;
    margin: 0 10px;
  }

  .tab-detail{
    padding: 10px;
  }

  ul{
    display: flex;
    padding: 0px;
    list-style: none;
  }

  li{
    margin: auto;
    cursor: pointer;
  }
</style>
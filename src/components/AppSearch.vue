<template>
  <div id="search" class="fullscreen">
    <header>
      <h1>
        Search
        <router-link to="/" class="exit-fullscreen"></router-link>
      </h1>
    </header>
    <main>
      <input type="search" id="search-input" v-model="searchInput" />

      <div class="table" v:if="$store.state.markerData">
        <div
          class="row"
          v-for="row in $store.state.markerData"
          v-bind:key="row.key"
          @click="rowClicked(row.label)"
        >
          <component
            :is="componentLoader"
            :rowData="row"
            v-show="!row.hidden"
          ></component>
        </div>
      </div>
    </main>
  </div>
</template>

<style lang="scss" scoped>
#search-input {
  width: calc(100% - 2rem);
  border-radius: 1em;
  font-size: 14pt;
  text-align: center;
  outline: 0;
}
.table {
  margin-top: 2rem;
  font-size: 10pt;
}
.table {
  width: calc(100% - 1rem);
  border-collapse: collapse;
}
.row {
  width: 100%;
}
</style>

<script>
import router from "../Router";
import { debounce } from "debounce";

export default {
  name: "AppSearch",

  data() {
    return {
      searchInput: null,
      searchFor: null,
    };
  },

  computed: {
    componentLoader() {
      return () =>
        import(
          "../apis/" + process.env.implementation + "/ItemInAppSearchTable"
        );
    },
  },

  watch: {
    searchInput: debounce(function (value) {
      this.searchFor = value;
    }, 600),

    searchFor: function () {
      this.$store.dispatch("markerMatch", this.searchFor.trim().toLowerCase());
    },
  },

  mounted() {
    this.componentLoader().then((comp) => {
      this.component = () => this.componentLoader();
    });
  },

  methods: {
    rowClicked: function (markerLabel) {
      this.$store.dispatch("focusMarkerByLabel", markerLabel);
      router.push("/");
    },
  },
};
</script>


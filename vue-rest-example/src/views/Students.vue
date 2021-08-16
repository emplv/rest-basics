<template>
  <div>
    <div v-if="list">
      <div v-if="meta.links">
        <button v-if="meta.links.first" @click="doRequest(meta.links.first)">
          First
        </button>
        <button v-if="meta.links.prev" @click="doRequest(meta.links.prev)">
          Prev
        </button>
        <button v-if="meta.links.next" @click="doRequest(meta.links.next)">
          Next
        </button>
        <button v-if="meta.links.last" @click="doRequest(meta.links.last)">
          Last
        </button>
      </div>
      <ul>
        <li
          v-for="item in list"
          :key="`list-item-${item.id}`"
          @click="openStudent(item.id)"
        >
          {{ item.first_name }} {{ item.last_name }}
        </li>
      </ul>
    </div>
    <div v-else-if="entry">
      <h3>{{ entry.first_name }}</h3>
      <p>Last name: {{ entry.last_name }} Email: {{ entry.email }}</p>
      <button @click="closeStudent">Go back to list</button>
    </div>
    <div v-else>
      Loading data...
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      list: undefined,
      entry: undefined,
      meta: undefined,
    };
  },
  methods: {
    doRequest(url, method = "GET") {
      fetch(url, {
        method,
      })
        .then((response) => {
          // console.log(response.headers.get('Content-Type'));
          // console.log(response.headers.get('Content-Total-Length'));
          return response.json();
        })
        .then((response) => {
          const { data, meta } = response;
          this.meta = meta;
          if (Array.isArray(data)) {
            this.list = data;
            this.entry = undefined;
          } else {
            this.entry = data;
            this.list = undefined;
          }
        });
    },
    openStudent(id) {
      this.$router.replace(`/students/${id}`);
    },
    closeStudent() {
      this.$router.replace(`/students`);
    },
  },
  watch: {
    $route(route) {
      const { id } = route.params;
      if (id) {
        const url = this.meta.single_entry.replace(":id", id);
        this.doRequest(url);
      } else {
        this.doRequest(this.meta.list_entries);
      }
    },
  },
  mounted() {
    const { id } = this.$route.params;
    if (id) {
      this.doRequest(`http://localhost:3000/api/students-management/students/${id}`);
    } else {
      this.doRequest("http://localhost:3000/api/students-management/students");
    }
  },
};
</script>

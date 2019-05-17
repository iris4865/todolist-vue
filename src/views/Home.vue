<template>
  <div class="home">{{ todolist }}</div>
</template>

<script>
// @ is an alias to /src
// import HelloWorld from '@/components/HelloWorld.vue';

import axios from 'axios';

export default {
  name: 'home',
  data() {
    return {
      todolist: [],
    };
  },

  methods: {
    getTodoList() {
      const path = 'http://localhost:5000/todolist';

      axios
        .get(path)
        .then((result) => {
          this.todolist = result.data;
        })
        .catch((error) => {
          console.error(error);
        });
    },
  },

  created() {
    this.getTodoList();
  },
};
</script>

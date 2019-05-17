<template>
  <b-container fluid>
    <h1>Todolist</h1>
    <div class="home">{{ todolist }}</div>
  </b-container>
</template>

<script>
// @ is an alias to /src
// import HelloWorld from '@/components/HelloWorld.vue';

import axios from 'axios';

export default {
  name: 'home',
  data() {
    return {
      todolist: [
        {
          id: 0,
          title: 'title1',
          contents: '1111',
          priority: 0,
          create_date: '2019.05.17 17:00:00',
          end_date: '2019.05.18 17:00:00',
          complete: true,
        },
      ],
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

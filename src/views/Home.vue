﻿<template>
  <b-container>
    <h1 class="text-center">TodoList</h1>
    <b-button
      v-b-modal.task-modal
      v-on:click="newTask()"
      class="btn btn-success btn-block">New Task</b-button>
    <hr>
    <b-row>
      <table class="table table-hover">
        <thead>
          <tr>
            <th scope="col">Title</th>
            <th scope="col">End Date</th>
            <th scope="col">Buttons</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(todo) in todolist"
              :key="todo.id"
              v-bind:class="{'table-danger': isExpire(todo.end_date, todo.complete)}">
            <td v-bind:class="{
                                'isComplete': todo.complete,
                                'table-danger': isExpire(todo.end_date, todo.complete)
                              }">{{ todo.title }}</td>
            <td style="width: 200px"
                v-bind:class="{
                                'isComplete': todo.complete,
                                'table-danger': isExpire(todo.end_date, todo.complete)}">
              {{ todo.end_date }}
            </td>
            <td style="width: 250px">
              <b-button v-on:click="upTask(todo.id)"
                        variant="info"
                        style="margin: 1px">
                ▲
              </b-button>
              <b-button v-on:click="downTask(todo.id)"
                        variant="info"
                        style="margin: 1px">
                ▼
              </b-button>
              <b-button
                v-b-modal.task-modal
                v-on:click="editTask(todo)"
                variant="warning"
                style="margin: 1px">Edit</b-button>
              <b-button v-on:click="deleteTask(todo.id)" variant="danger">Delete</b-button>
            </td>
          </tr>
        </tbody>
      </table>
    </b-row>

    <b-modal ref="taskModal" id="task-modal" title="New" hide-footer>
      <TaskForm v-bind:task="task"
                @okTask="okTask"
                @cancelTask="cancelTask"/>
    </b-modal>
  </b-container>
</template>

<script>

import axios from 'axios';
import TaskForm from '@/components/TaskForm.vue';

export default {
  name: 'home',
  components: {
    TaskForm,
  },

  data() {
    return {
      new_task_title: '',

      task: {
        id: 0,
        title: '',
        description: '',
        priority: 0,
        create_date: '',
        end_date: '',
        complete: false,
      },

      todolist: [],
    };
  },

  methods: {
    getTodoList() {
      const path = 'api/task/';

      axios
        .get(path)
        .then((result) => {
          this.todolist = result.data.sort((x, y) => ((x.priority < y.priority) ? -1 : 1));
        })
        .catch((error) => {
          // eslint-disable-next-line
          console.error(error);
        });
    },

    isExpire(date, isComplete) {
      if (isComplete || date === '') {
        return false;
      }

      const end = new Date(date);
      const now = new Date();

      if (end - now > 0) {
        return false;
      }

      return true;
    },

    newTask() {
      this.task = {
        id: -1,
        title: '',
        description: '',
        priority: 0,
        create_date: '',
        end_date: '',
        complete: false,
      };
    },

    okTask() {
      this.$refs.taskModal.hide();

      const payload = {
        title: this.task.title,
        description: this.task.description,
        end_date: this.task.end_date,
        complete: this.task.complete,
      };

      if (this.task.id === -1) { // new
        this.addTask(payload);
      } else {
        this.updateTask(this.task.id, payload);
      }
    },

    cancelTask() {
      this.$refs.taskModal.hide();
      this.getTodoList();
    },

    upTask(id) {
      for (let index = 1; index < this.todolist.length; index += 1) {
        if (id === this.todolist[index].id) {
          const now = this.todolist[index];
          const prev = this.todolist[index - 1];
          this.updateTask(now.id, { priority: prev.priority });
          this.updateTask(prev.id, { priority: now.priority });
          break;
        }
      }
    },

    downTask(id) {
      for (let index = 0; index < this.todolist.length - 1; index += 1) {
        if (id === this.todolist[index].id) {
          const now = this.todolist[index];
          const next = this.todolist[index + 1];
          this.updateTask(now.id, { priority: next.priority });
          this.updateTask(next.id, { priority: now.priority });
          break;
        }
      }
    },

    editTask(task) {
      this.task = {
        id: task.id,
        title: task.title,
        description: task.description,
        end_date: task.end_date,
        complete: task.complete,
      };
    },

    addTask(payload) {
      const path = 'api/task/';
      axios
        .post(path, payload)
        .then((res) => {
          this.new_task_title = '';
          this.message = res.data.message;
          this.getTodoList();
        })
        .catch((error) => {
          // eslint-disable-next-line
          console.log(error);
          this.getTodoList();
        });

      this.getTodoList();
    },

    updateTask(taskID, payload) {
      const path = `api/task/${taskID}`;
      axios
        .put(path, payload)
        .then((res) => {
          this.new_task_title = '';
          this.message = res.data.message;
          this.getTodoList();
        })
        .catch((error) => {
          // eslint-disable-next-line
          console.error(error);
          this.getTodoList();
        });
    },

    deleteTask(taskID) {
      const path = `api/task/${taskID}`;
      axios
        .delete(path)
        .then((res) => {
          this.message = res.data.message;
          this.getTodoList();
        })
        .catch((error) => {
          // eslint-disable-next-line
          console.error(error);
          this.getTodoList();
        });
    },
  },

  created() {
    this.getTodoList();
  },
};
</script>

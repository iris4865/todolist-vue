<template>
  <b-container>
    <h1 class="text-center">TodoList</h1>
    <b-button
      v-b-modal.new-task-modal
      v-on:click="newTask()"
      class="btn btn-success btn-block"
    >New Task</b-button>
    <hr>
    <b-row>
      <table class="table">
        <tr v-for="(todo) in todolist" :key="todo.id">
          <td class="text-left"
              v-bind:class="{'isComplete': todo.complete}">{{ todo.title }}</td>
          <td class="text-right"
              v-bind:class="{'isComplete': todo.complete}">{{ todo.end_date }}</td>
          <td class="text-right">
            <b-button
              v-b-modal.update-task-modal
              v-on:click="editTask(todo)"
              class="btn btn-warning btn-sm"
            >Edit</b-button>
            <b-button v-on:click="deleteTask(todo.id)" class="btn btn-danger btn-sm">Delete</b-button>
          </td>
        </tr>
      </table>
    </b-row>

    <b-modal ref="newTaskModal" id="new-task-modal" title="New" hide-footer>
      <b-form @submit.prevent="onAddTask" @reset.prevent="onResetNewTask" class="w-100">
        <b-form-group id="form-title-new-group" label="Title:" label-for="form-title-new-input">
          <b-form-input
            id="form-title-new-input"
            type="text"
            v-model="task.title"
            required
            placeholder="Enter title"
          ></b-form-input>
        </b-form-group>
        <b-form-group
          id="form-description-new-group"
          label="Description:"
          label-for="form-author-new-input"
        >
          <b-form-textarea
            id="form-description-new-textarea"
            v-model="task.description"
            placeholder="Enter description"
          ></b-form-textarea>
        </b-form-group>
        <b-form-group
          id="form-end_date-new-group"
          label="End Date:"
          label-for="form-author-new-input"
        >
          <b-form-input
            id="form-end_date-new-input"
            type="date"
            v-model="task.end_date"
            placeholder="Enter End Date"
          ></b-form-input>
        </b-form-group>
        <b-form-checkbox id="form-complete-checkbox" v-model="task.complete">Complete Task</b-form-checkbox>

        <b-button-group>
          <b-button type="submit" variant="primary">OK</b-button>
          <b-button type="reset" variant="danger">Cancel</b-button>
        </b-button-group>
      </b-form>
    </b-modal>

    <b-modal ref="editTaskModal" id="update-task-modal" title="Update" hide-footer>
      <b-form @submit.prevent="onUpdateTask" @reset.prevent="onResetTask" class="w-100">
        <b-form-group id="form-title-edit-group" label="Title:" label-for="form-title-edit-input">
          <b-form-input
            id="form-title-edit-input"
            type="text"
            v-model="task.title"
            required
            placeholder="Enter title"
          ></b-form-input>
        </b-form-group>
        <b-form-group
          id="form-description-edit-group"
          label="Description:"
          label-for="form-author-edit-input"
        >
          <b-form-input
            id="form-description-edit-input"
            type="text"
            v-model="task.description"
            required
            placeholder="Enter description"
          ></b-form-input>
        </b-form-group>
        <b-form-group
          id="form-end_date-edit-group"
          label="End Date:"
          label-for="form-author-edit-input"
        >
          <b-form-input
            id="form-end_date-edit-input"
            type="date"
            v-model="task.end_date"
            required
            placeholder="Enter End Date"
          ></b-form-input>
        </b-form-group>
        <b-form-checkbox id="form-complete-checkbox" v-model="task.complete">Complete Task</b-form-checkbox>

        <b-button-group>
          <b-button type="submit" variant="primary">Update</b-button>
          <b-button type="reset" variant="danger">Cancel</b-button>
        </b-button-group>
      </b-form>
    </b-modal>
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

      todolist: [
        {
          id: 0,
          title: 'title1',
          description: '1111',
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
      const path = 'http://localhost:5000/api/task/';

      axios
        .get(path)
        .then((result) => {
          this.todolist = result.data;
        })
        .catch((error) => {
          // eslint-disable-next-line
          console.error(error);
        });
    },

    newTask() {
      this.task = this.initTask();
    },

    onAddTask() {
      this.$refs.newTaskModal.hide();
      const payload = {
        title: this.task.title,
        description: this.task.description,
        end_date: this.task.end_date,
        complete: this.task.complete,
      };
      this.addTask(payload);
    },

    addTask(payload) {
      const path = 'http://localhost:5000/api/task/';
      axios
        .post(path, payload)
        .then((res) => {
          this.getTodoList();
          this.new_task_title = '';
          this.message = res.data.message;
        })
        .catch((error) => {
          // eslint-disable-next-line
          console.log(error);
          this.getTodoList();
        });

      this.getTodoList();
    },

    editTask(task) {
      this.task = this.copyTask(task);
    },

    onUpdateTask() {
      this.$refs.editTaskModal.hide();
      const payload = {
        title: this.task.title,
        description: this.task.description,
        end_date: this.task.end_date,
        complete: this.task.complete,
      };
      this.updateTask(this.task.id, payload);
    },

    updateTask(taskID, payload) {
      const path = `http://localhost:5000/api/task/${taskID}`;
      axios
        .put(path, payload)
        .then((res) => {
          this.getTodoList();
          this.new_task_title = '';
          this.message = res.data.message;
        })
        .catch((error) => {
          // eslint-disable-next-line
          console.error(error);
          this.getTodoList();
        });
    },

    deleteTask(taskID) {
      const path = `http://localhost:5000/api/task/${taskID}`;
      axios
        .delete(path)
        .then((res) => {
          this.getTodoList();
          this.message = res.data.message;
        })
        .catch((error) => {
          this.getTodoList();
          // eslint-disable-next-line
          console.error(error);
        });
    },

    onResetNewTask() {
      this.$refs.newTaskModal.hide();
      this.getTodoList();
    },

    onResetTask() {
      this.$refs.editTaskModal.hide();
      this.getTodoList();
    },

    initTask() {
      return {
        id: 0,
        title: '',
        description: '',
        priority: 0,
        create_date: '',
        end_date: '',
        complete: false,
      };
    },

    copyTask(task) {
      return {
        id: task.id,
        title: task.title,
        description: task.description,
        end_date: task.end_date,
        complete: task.complete,
      };
    },
  },

  created() {
    this.getTodoList();
  },
};
</script>

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
      <table class="table table-hover">
        <thead>
          <tr>
            <th scope="col">Title</th>
            <th scope="col">End Date</th>
            <th scope="col">Buttons</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(todo) in todolist" :key="todo.id" v-bind:class="{'table-danger': isExpire(todo.end_date, todo.complete)}" >
            <td
                v-bind:class="{'isComplete': todo.complete,
                              'table-danger': isExpire(todo.end_date, todo.complete)}"
            >{{ todo.title }}</td>

            <td style="width: 200px"
                v-bind:class="{'isComplete': todo.complete,
                              'table-danger': isExpire(todo.end_date, todo.complete)}"
            >{{ todo.end_date }}</td>

            <td style="width: 250px">
              <b-button v-on:click="upTask(todo.id)" variant="info" style="margin: 1px">▲</b-button>
              <b-button v-on:click="downTask(todo.id)" variant="info" style="margin: 1px">▼</b-button>
              <b-button
                v-b-modal.update-task-modal
                v-on:click="editTask(todo)"
                variant="warning"
                style="margin: 1px"
              >Edit</b-button>
              <b-button v-on:click="deleteTask(todo.id)" variant="danger">Delete</b-button>
            </td>
          </tr>
        </tbody>
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
          <b-form-textarea
            id="form-description-edit-textarea"
            v-model="task.description"
            placeholder="Enter description"
          ></b-form-textarea>
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
      if (isComplete) {
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
        id: 0,
        title: '',
        description: '',
        priority: 0,
        create_date: '',
        end_date: '',
        complete: false,
      };
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

    upTask(id) {
      for (let index = 1; index < this.todolist.length; index++) {
        if (id == this.todolist[index].id) {
          const now = this.todolist[index];
          const prev = this.todolist[index - 1];
          this.updateTask(now.id, { priority: prev.priority });
          this.updateTask(prev.id, { priority: now.priority });
          break;
        }
      }
    },

    downTask(id) {
      for (let index = 0; index < this.todolist.length - 1; index++) {
        if (id == this.todolist[index].id) {
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

    onResetNewTask() {
      this.$refs.newTaskModal.hide();
      this.getTodoList();
    },

    onResetTask() {
      this.$refs.editTaskModal.hide();
      this.getTodoList();
    },
  },

  created() {
    this.getTodoList();
  },
};
</script>

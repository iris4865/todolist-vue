<template>
  <b-form @submit.prevent="onOkTask" @reset.prevent="onCancelTask" class="w-100">
    <b-form-group id="form-title-new-group" label="Title:" label-for="form-title-new-input">
      <b-form-input
        id="form-title-new-input"
        type="text"
        v-model="task.title"
        required
        placeholder="Enter title"/>
    </b-form-group>
    <b-form-group
      id="form-description-new-group"
      label="Description:"
      label-for="form-author-new-input">
      <b-form-textarea
        id="form-description-new-textarea"
        v-model="task.description"
        placeholder="Enter description"/>
    </b-form-group>
    <b-form-group id="form-end_date-new-group" label="End Date:" label-for="form-author-new-input">
      <b-form-input
        id="form-end_date-new-input"
        type="date"
        v-model="task.end_date"
        placeholder="Enter End Date"/>
    </b-form-group>
    <b-form-checkbox id="form-complete-checkbox"
                     v-model="task.complete">
      Complete Task
    </b-form-checkbox>

    <b-button-group>
      <b-button type="submit" variant="primary">OK</b-button>
      <b-button type="reset" variant="danger">Cancel</b-button>
    </b-button-group>
  </b-form>
</template>

<script>
export default {
  name: 'TaskForm',
  props: {
    task: Object,
  },

  data() {
    return {};
  },

  methods: {
    onOkTask() {
      this.$emit('okTask');
    },

    onCancelTask() {
      this.$emit('cancelTask');
    },
  },
};
</script>

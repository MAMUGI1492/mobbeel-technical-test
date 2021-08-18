<template>
  <q-card class="file-container" bordered flat>
    <q-card-section>
      <q-form @submit.prevent="onSubmit">
        <q-file
          :model-value="file"
          @update:model-value="onUpdate"
          accept="image/jpeg"
          label="Pick one image"
          name="image"
          outlined
        />

        <div class="flex justify-end">
          <q-btn
            :loading="loading"
            color="primary"
            label="Submit"
            type="submit"
          />
        </div>
      </q-form>
    </q-card-section>
  </q-card>
</template>

<script>
import { defineComponent } from "vue";

export default defineComponent({
  name: "FileContainer",
  props: {
    file: { required: true, type: Object },
    loading: { required: true, type: Boolean },
  },
  emits: ["update:file", "submit"],
  setup(props, { emit }) {
    return {
      onSubmit(evt) {
        const formData = new FormData(evt.target);

        emit("submit", formData);
      },
      onUpdate(newValue) {
        emit("update:file", newValue);
      },
    };
  },
});
</script>

<style lang="scss" scoped>
.file-container .q-form {
  display: flex;
  flex-direction: column;
  gap: $flex-gutter-md;
}
</style>

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

        <q-option-group
          v-model="side"
          :options="options"
          color="primary"
          inline
        />

        <div class="button-containers">
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
import { defineComponent, ref } from "vue";

export default defineComponent({
  name: "FileContainer",
  props: {
    file: { required: true, type: Object },
    loading: { required: true, type: Boolean },
  },
  emits: ["update:file", "submit"],
  setup(props, { emit }) {
    const side = ref(0);

    return {
      side,
      options: [
        {
          label: "Document front side",
          value: 0,
        },
        {
          label: "Document back side",
          value: 1,
        },
      ],
      onSubmit(evt) {
        const formData = new FormData(evt.target);

        formData.append("documentSide", side.value);

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

  .button-containers {
    display: flex;
    justify-content: flex-end;
  }
}
</style>

<template>
  <q-card class="file-container" bordered flat>
    <q-card-section>
      <q-form @submit.prevent="onSubmit">
        <q-file
          v-model="file"
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
    loading: { required: true, type: Boolean },
  },
  setup(props, { emit }) {
    const file = ref(null);
    const side = ref(0);

    const options = [
      {
        label: "Document front side",
        value: 0,
      },
      {
        label: "Document back side",
        value: 1,
      },
    ];

    const onSubmit = (evt) => {
      const formData = new FormData(evt.target);

      formData.append("documentSide", side.value);

      emit("submit", formData);

      file.value = null;
    };

    return {
      file,
      side,
      options,
      onSubmit,
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

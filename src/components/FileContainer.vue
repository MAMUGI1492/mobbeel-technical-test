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

        <option-group v-model.side="side" />

        <div class="button-containers">
          <q-btn
            :loading="isLoading"
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
import OptionGroup from "src/components/OptionGroup.vue";

export default defineComponent({
  name: "FileContainer",
  components: { OptionGroup },
  props: {
    isLoading: { required: true, type: Boolean },
  },
  emits: ["submit"],
  setup(props, { emit }) {
    const file = ref(null);
    const side = ref(0);

    const onSubmit = (evt) => {
      const formData = new FormData(evt.target);

      formData.append("documentSide", side.value);

      emit("submit", formData);

      file.value = null;
    };

    return { file, side, onSubmit };
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

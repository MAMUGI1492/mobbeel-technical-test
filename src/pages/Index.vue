<template>
  <q-page class="page-index">
    <q-card bordered flat>
      <q-card-section>
        <q-form @submit.prevent="onSubmit">
          <q-file
            v-model="file"
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

    <q-card v-if="responseImage" class="image-card" bordered flat>
      <q-card-section>
        <q-img :src="responseImage" fit="contain" width="300px" />
      </q-card-section>
    </q-card>
  </q-page>
</template>

<script>
import { defineComponent, ref } from "vue";
import { useQuasar } from "quasar";
import { post } from "axios";

export default defineComponent({
  name: "PageIndex",
  setup() {
    const $q = useQuasar();

    const file = ref(null);
    const loading = ref(false);
    const responseImage = ref(null);

    const postImage = (formData) => {
      loading.value = true;

      const url = "https://demo.mobbeel.com/mobbscan/detectDocument.json";

      const headers = {
        "Content-Type": "multipart/form-data;",
      };

      formData.append("licenseId", "mobbscan-challenge");
      formData.append("documentType", "ESP");
      formData.append("documentSide", 0);
      formData.append("returnDocument", true);

      post(url, formData, { headers })
        .then(({ data }) => {
          if (data.code === "OK") {
            responseImage.value = `data:image/jpg;base64,${data.imageDocument}`;

            file.value = null;

            $q.notify({
              message: "ID document detected!",
              position: "top-right",
              type: "positive",
            });
          }

          if (data.code === "ERROR") {
            file.value = null;

            $q.notify({
              message: "ID document not detected!",
              position: "top-right",
              type: "negative",
            });
          }
        })
        .catch((error) => console.error(error))
        .finally(() => (loading.value = false));
    };

    return {
      file,

      onSubmit(evt) {
        const formData = new FormData(evt.target);

        postImage(formData);
      },

      loading,

      responseImage,
    };
  },
});
</script>

<style lang="scss" scoped>
@import "/src/css/app.scss";

.page-index {
  @include flex-center;
  flex-direction: column;
  gap: $flex-gutter-md;

  .q-card .q-form {
    display: flex;
    flex-direction: column;
    gap: $flex-gutter-md;
  }
}
</style>

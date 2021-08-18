<template>
  <q-page class="page-index">
    <file-container
      v-model:file="file"
      :loading="loading"
      @submit="postImage"
    />

    <image-container v-if="responseImage" :src="responseImage" />
  </q-page>
</template>

<script>
import { defineComponent, ref } from "vue";
import { useQuasar } from "quasar";
import { post } from "axios";
import FileContainer from "src/components/FileContainer.vue";
import ImageContainer from "src/components/ImageContainer.vue";

export default defineComponent({
  name: "PageIndex",
  components: {
    FileContainer,
    ImageContainer,
  },
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
      loading,
      responseImage,
      postImage,
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
}
</style>

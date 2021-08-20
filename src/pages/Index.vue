<template>
  <q-page class="page-index" padding>
    <q-card class="tab-panel">
      <q-tabs
        v-model="tab"
        align="justify"
        class="text-primary"
        narrow-indicator
      >
        <q-tab name="file">
          <q-icon name="file_upload" size="sm" />
          <span>File</span>
        </q-tab>

        <q-tab name="camera">
          <q-icon name="photo_camera" size="sm" />
          <span>Camera</span>
        </q-tab>
      </q-tabs>

      <q-tab-panels v-model="tab" animated swipeable>
        <q-tab-panel name="file">
          <file-container
            :is-loading="isLoading"
            @change-loading="changeLoading"
            @submit="postImage"
          />
        </q-tab-panel>

        <q-tab-panel name="camera">
          <camera-container :is-loading="isLoading" @submit="postImage" />
        </q-tab-panel>
      </q-tab-panels>
    </q-card>

    <image-container v-if="responseImage" :src="responseImage" />
  </q-page>
</template>

<script>
import { defineComponent, ref } from "vue";
import { useQuasar } from "quasar";
import { post } from "axios";
import FileContainer from "src/components/FileContainer.vue";
import CameraContainer from "src/components/CameraContainer.vue";
import ImageContainer from "src/components/ImageContainer.vue";

export default defineComponent({
  name: "PageIndex",
  components: {
    CameraContainer,
    FileContainer,
    ImageContainer,
  },
  setup() {
    const $q = useQuasar();

    const isLoading = ref(false);
    const responseImage = ref(null);

    const changeLoading = (newState) => (isLoading.value = newState);

    const postImage = (formData) => {
      responseImage.value = null;
      changeLoading(true);

      const url = "https://demo.mobbeel.com/mobbscan/detectDocument.json";

      const headers = {
        "Content-Type": "multipart/form-data;",
      };

      formData.append("licenseId", "mobbscan-challenge");
      formData.append("documentType", "ESP");
      formData.append("returnDocument", true);

      post(url, formData, { headers })
        .then(({ data }) => {
          if (data.code === "OK") {
            responseImage.value = `data:image/jpg;base64,${data.imageDocument}`;

            $q.notify({
              message: "ID document detected!",
              position: "top-right",
              type: "positive",
            });
          }

          if (data.code === "ERROR") {
            $q.notify({
              message: "ID document not detected!",
              position: "top-right",
              type: "negative",
            });
          }
        })
        .catch((error) => console.error(error))
        .finally(() => changeLoading(false));
    };

    return {
      tab: ref("file"),
      isLoading,
      changeLoading,
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

  > .q-card {
    &.tab-panel {
      width: 85%;
    }

    .q-tabs .q-tab ::v-deep(.q-tab__content) {
      display: flex;
      flex-direction: row;
      gap: $flex-gutter-sm;
    }
  }
}
</style>

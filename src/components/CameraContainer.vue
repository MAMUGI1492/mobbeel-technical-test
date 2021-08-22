<template>
  <q-card class="camera-container" bordered flat>
    <q-card-section>
      <video v-if="isCameraOpen" v-show="!isLoading" ref="video" autoplay />

      <canvas v-if="isCameraOpen" v-show="isLoading" ref="canvas" />

      <option-group v-if="isCameraOpen" v-model.side="side" />

      <div class="button-containers">
        <q-btn :flat="isCameraOpen" @click="toggleCamera" color="primary">
          <span v-if="isCameraOpen">Close Camera</span>
          <span v-else>Open Camera</span>
        </q-btn>

        <q-btn
          v-if="isCameraOpen"
          :loading="isLoading"
          @click="takePhoto"
          color="primary"
          icon="camera"
          round
        />
      </div>
    </q-card-section>
  </q-card>
</template>

<script>
import { useQuasar } from "quasar";
import { defineComponent, ref } from "vue";
import OptionGroup from "src/components/OptionGroup.vue";

export default defineComponent({
  name: "CameraContainer",
  components: { OptionGroup },
  props: {
    isLoading: { required: true, type: Boolean },
  },
  emits: ["change-loading", "submit"],
  setup(props, { emit }) {
    const $q = useQuasar();
    const isMobile = !!$q.platform.is.mobile;

    const isCameraOpen = ref(true);

    const video = ref(null);
    const canvas = ref(null);

    const side = ref(0);

    const toggleCamera = () =>
      isCameraOpen.value ? stopCameraStream() : createCameraElement();

    const createCameraElement = () => {
      isCameraOpen.value = true;

      const videoConstrains = () => {
        const size = { width: { ideal: 1920 }, height: { ideal: 1080 } };
        const rearCamera = { facingMode: { exact: "environment" } };

        return isMobile ? { ...rearCamera, ...size } : size;
      };

      const constraints = { audio: false, video: videoConstrains() };

      navigator.mediaDevices
        .getUserMedia(constraints)
        .then((mediaStream) => (video.value.srcObject = mediaStream))
        .catch((error) => console.error(error));
    };

    const takePhoto = () => {
      emit("change-loading", true);

      const { videoWidth, videoHeight } = video.value;
      canvas.value.width = videoWidth;
      canvas.value.height = videoHeight;
      const context = canvas.value.getContext("2d");
      context.drawImage(video.value, 0, 0, videoWidth, videoHeight);

      canvas.value.toBlob(onSubmit, "image/jpeg");
    };

    const onSubmit = (blob) => {
      const formData = new FormData();

      formData.append("image", blob);
      formData.append("documentSide", side.value);

      emit("submit", formData);
    };

    const stopCameraStream = () => {
      isCameraOpen.value = false;

      let tracks = video.value.srcObject.getTracks();

      tracks.forEach((track) => track.stop());
    };

    createCameraElement();

    return { canvas, isCameraOpen, side, toggleCamera, takePhoto, video };
  },
});
</script>

<style lang="scss" scoped>
.camera-container {
  .q-card__section {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    gap: $flex-gutter-md;

    video,
    canvas {
      max-width: 100%;
    }

    .button-containers {
      display: flex;
      justify-content: center;
      gap: $flex-gutter-md;
    }
  }
}
</style>

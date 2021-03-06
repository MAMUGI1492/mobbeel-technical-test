<template>
  <q-card class="camera-container" bordered flat>
    <q-card-section>
      <div class="button-containers">
        <q-btn @click="toggleCamera" color="primary">
          <span v-if="isCameraOpen">Close Camera</span>
          <span v-else>Open Camera</span>
        </q-btn>

        <q-btn-toggle
          v-if="showOptionGroup"
          v-model="cameraType"
          :options="cameraTypes"
          toggle-color="primary"
        >
          <template v-slot:front>
            <q-icon name="photo_camera_front" />
          </template>

          <template v-slot:rear>
            <q-icon name="video_camera_back" />
          </template>
        </q-btn-toggle>
      </div>

      <video v-if="isCameraOpen" v-show="!isLoading" ref="video" autoplay />

      <canvas v-if="isCameraOpen" v-show="isLoading" ref="canvas" />

      <option-group v-if="isCameraOpen" v-model.side="side" />

      <q-btn
        v-if="isCameraOpen"
        :loading="isLoading"
        @click="takePhoto"
        color="primary"
        icon="camera"
        round
      />
    </q-card-section>
  </q-card>
</template>

<script>
import { useQuasar } from "quasar";
import { computed, defineComponent, ref, watch } from "vue";
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
    const cameraType = ref("front");
    const cameraTypes = [
      { value: "front", slot: "front" },
      { value: "rear", slot: "rear" },
    ];

    const showOptionGroup = computed(() => isCameraOpen.value && isMobile);

    const video = ref(null);
    const canvas = ref(null);

    const side = ref(0);

    const toggleCamera = () =>
      isCameraOpen.value ? stopCameraStream() : createCameraElement();

    const createCameraElement = () => {
      isCameraOpen.value = true;

      const videoConstrains = () => {
        const size = { width: { ideal: 1920 }, height: { ideal: 1080 } };
        const isRearCamera = cameraType.value === "front";
        const cameraTypeConstraints = {
          facingMode: isRearCamera ? "user" : { exact: "environment" },
        };

        return isMobile ? { ...cameraTypeConstraints, ...size } : size;
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

    watch(cameraType, () => {
      toggleCamera();

      toggleCamera();
    });

    return {
      canvas,
      cameraType,
      cameraTypes,
      isCameraOpen,
      showOptionGroup,
      side,
      toggleCamera,
      takePhoto,
      video,
    };
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

    .button-containers {
      display: flex;
      justify-content: center;
      gap: $flex-gutter-md;
    }

    video,
    canvas {
      max-width: 100%;
    }
  }
}
</style>

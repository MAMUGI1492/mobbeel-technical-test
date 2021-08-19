<template>
  <q-card class="camera-container" bordered flat>
    <q-card-section>
      <q-btn color="primary" @click="toggleCamera">
        <span v-if="!isCameraOpen">Open Camera</span>
        <span v-else>Close Camera</span>
      </q-btn>

      <div
        v-if="isCameraOpen"
        v-show="!isLoading"
        class="camera-box"
        :class="{ flash: isShotPhoto }"
      >
        <div class="camera-shutter" :class="{ flash: isShotPhoto }"></div>

        <video
          v-show="!isPhotoTaken"
          :height="337.5"
          :width="450"
          ref="camera"
          autoplay
        ></video>

        <canvas
          v-show="isPhotoTaken"
          :height="337.5"
          :width="450"
          id="photoTaken"
          ref="canvas"
        ></canvas>
      </div>

      <q-btn
        v-if="isCameraOpen"
        :loading="isLoading || loading"
        color="primary"
        icon="camera"
        @click="takePhoto"
      />
    </q-card-section>
  </q-card>
</template>

<script>
import { defineComponent, ref } from "vue";

export default defineComponent({
  name: "CameraContainer",
  props: {
    loading: { required: true, type: Boolean },
  },
  emits: ["submit"],
  setup(props, { emit }) {
    const isCameraOpen = ref(false);
    const isPhotoTaken = ref(false);
    const isShotPhoto = ref(false);
    const isLoading = ref(false);

    const camera = ref(null);
    const canvas = ref(null);

    const toggleCamera = () => {
      if (isCameraOpen.value) {
        isCameraOpen.value = false;
        isPhotoTaken.value = false;
        isShotPhoto.value = false;
        stopCameraStream();
      } else {
        isCameraOpen.value = true;
        createCameraElement();
      }
    };

    const createCameraElement = () => {
      isLoading.value = true;

      const constraints = (window.constraints = {
        audio: false,
        video: true,
      });

      navigator.mediaDevices
        .getUserMedia(constraints)
        .then((stream) => (camera.value.srcObject = stream))
        .catch((error) => console.error(error))
        .finally(() => (isLoading.value = false));
    };

    const stopCameraStream = () => {
      let tracks = camera.value.srcObject.getTracks();

      tracks.forEach((track) => track.stop());
    };

    const takePhoto = () => {
      if (!isPhotoTaken.value) {
        isShotPhoto.value = true;

        const FLASH_TIMEOUT = 50;

        setTimeout(() => {
          isShotPhoto.value = false;
        }, FLASH_TIMEOUT);
      }

      isPhotoTaken.value = !isPhotoTaken.value;

      const context = canvas.value.getContext("2d");
      context.drawImage(camera.value, 0, 0, 450, 337.5);

      onSubmit(canvas.value);
    };

    const dataURItoBlob = (dataURI) => {
      // convert base64/URLEncoded data component to raw binary data held in a string
      let byteString;
      if (dataURI.split(",")[0].indexOf("base64") >= 0)
        byteString = atob(dataURI.split(",")[1]);
      else byteString = unescape(dataURI.split(",")[1]);

      // separate out the mime component
      let mimeString = dataURI.split(",")[0].split(":")[1].split(";")[0];

      // write the bytes of the string to a typed array
      let ia = new Uint8Array(byteString.length);
      for (var i = 0; i < byteString.length; i++) {
        ia[i] = byteString.charCodeAt(i);
      }

      return new Blob([ia], { type: mimeString });
    };

    const onSubmit = () => {
      const dataURL = document
        .getElementById("photoTaken")
        .toDataURL("image/jpeg");

      const blob = dataURItoBlob(dataURL);

      const formData = new FormData();

      formData.append("image", blob);
      formData.append("documentSide", 0);

      emit("submit", formData);

      setTimeout(() => {
        toggleCamera();
      }, 1500);
    };

    return {
      isCameraOpen,
      isPhotoTaken,
      isShotPhoto,
      isLoading,
      camera,
      canvas,
      toggleCamera,
      takePhoto,
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
    width: 500px;
  }

  .camera-box {
    .camera-shutter {
      opacity: 0;
      width: 450px;
      height: 337.5px;
      background-color: #fff;
      position: absolute;

      &.flash {
        opacity: 1;
      }
    }
  }
}
</style>

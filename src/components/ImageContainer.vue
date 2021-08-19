<template>
  <q-card class="image-container" bordered flat>
    <q-card-section>
      <div class="text-h6">Document detected:</div>
    </q-card-section>

    <q-separator dark inset />

    <q-card-section>
      <q-img :src="src" fit="contain" width="300px" />
    </q-card-section>
  </q-card>
</template>

<script>
import { defineComponent } from "vue";

export default defineComponent({
  name: "ImageContainer",
  props: {
    src: { required: true, type: String },
  },
});
</script>

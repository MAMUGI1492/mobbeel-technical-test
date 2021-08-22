<template>
  <q-option-group
    :model-value="$props.side"
    :options="options"
    @update:model-value="onUpdateSide"
    color="primary"
    inline
  />
</template>

<script>
import { defineComponent } from "vue";

export default defineComponent({
  name: "OptionGroup",
  props: {
    side: {
      required: true,
      type: Number,
      validator: (value) => [0, 1].includes(value),
    },
  },
  setup(props, { emit }) {
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

    const onUpdateSide = (newValue) => emit("update:side", newValue);

    return { onUpdateSide, options };
  },
});
</script>

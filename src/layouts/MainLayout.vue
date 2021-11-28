<template>
  <div class="root">
    <div class="row justify-center q-mb-sm">
      <div class="text-h2">Cover Letter Writer</div>
    </div>
    <div class="row justify-center">
      <div class="caption">By oceansam</div>
    </div>
    <cover-letter-input />
  </div>
</template>

<script>
import { ref } from "vue";
// Components
import CoverLetterInput from "src/components/CoverLetterInput.vue";
export default {
  name: "MainLayout",
  components: {
    CoverLetterInput,
  },
  setup() {
    const inputField = ref();

    return {
      inputField,
    };
  },
};
</script>

<style lang="scss" scoped>
.root {
  margin: 20px 50px 0 50px;
}
</style>

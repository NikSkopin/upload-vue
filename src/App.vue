<template>
  <div class="section">
    <div class="container">
      <div class="columns">
        <div class="column">
          <SimpleUpload />
        </div>
        <div class="column">
          <MultipleUpload />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import SimpleUpload from "./components/SimpleUpload.vue";
import MultipleUpload from "./components/MultipleUpload.vue";

export default {
  name: "App",
  components: { SimpleUpload, MultipleUpload }
};
</script>
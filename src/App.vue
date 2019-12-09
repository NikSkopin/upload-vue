<template>
  <div class="section">
    <div class="container">
      <div class="columns is-centered">
        <div class="column is-two-thirds">
          <div class="box has-text-centered">
            <MultipleUpload />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import MultipleUpload from "./components/MultipleUpload.vue";

export default {
  name: "App",
  components: { MultipleUpload }
};
</script>
<style >
.centered-label {
  width: 70%;
  margin: 0 auto;
}
</style>

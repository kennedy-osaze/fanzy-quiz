<template>
  <b-container>
    <b-row>
      <b-col sm="12">
        <div class="text-center mt-5 mb-2">
          <h6 class="display-4 h6-d4-fs">Welcome to Fanzy Quiz App</h6>

          <p class="mt-3">Where you get to answer various kinds of questions depending on the question category/domain, the question size and difficulty of your own choosing.</p>

          <div class="mt-5">
            <b-button variant="primary" @click="$emit('begin')">Let's Begin</b-button>
          </div>
        </div>
      </b-col>
    </b-row>
  </b-container>
</template>

<script>
export default {
  name: "home",
}
</script>

<style scoped>

</style>

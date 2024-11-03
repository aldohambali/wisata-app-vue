<template>
  <v-container>
    <div class="text-center pa-5 my-5">
      <h1>Welcome to Wisata App</h1>
      <p>Book your best deals today!</p>
      <v-btn             
        class="text-none mt-5 "
        color="#1a73e8"
        variant="flat"            
        @click="goStay"
      >Book</v-btn>
    </div>

  </v-container>
</template>


<script>
export default {
  methods: {
    goStay() {
      this.$router.push('/stay');
    },
  },
};
</script>

<style scoped>
h1 {
  color: #1976d2;
}
</style>
<template>
  <div class="home">
    <header>
      <div class="backgroundImage"></div>
      
    </header>
    <main></main>
  </div>
</template>

<script>

export default {
  name: 'HomeView',
  components: {
    
  }
}
</script>


<style lang="scss" scoped>

  .home {
    .backgroundImage {
      width: 100%;
      height: 300px;
      background-image: url('@/assets/img/cityBackground.png');
      background-position: top;
      background-repeat: no-repeat;
      background-size: cover;
    }
  }

</style>

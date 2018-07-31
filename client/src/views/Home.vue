<template>
  <div class="home">
    <div class="navBody" style="height:730px;">
      <ForNav/>
    <br>
      <ForBody/>
    </div>
    <br>
    <ForFooter/>
  </div>
</template>

<script>
// @ is an alias to /src
import ForNav from '@/components/Navbar.vue'
import ForBody from '@/components/Body.vue'
import ForFooter from '@/components/Footer.vue'

export default {
  name: 'home',
  components: {
    ForNav, ForBody, ForFooter
  }
}
</script>

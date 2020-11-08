<template>
  <div class="home">
    <div>
      <Products/>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import Products from '../components/Products'
export default {
  name: 'Home',
  components: {
    Products
  }
}
</script>

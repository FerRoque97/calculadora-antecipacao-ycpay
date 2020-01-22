<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <HelloWorld msg="Calculadora de Antecipação da YC Pay"/>
    <Calculadora/>
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'
import Calculadora from '@/components/Calculadora.vue'

export default {
  name: 'home',
  components: {
    HelloWorld,
    Calculadora
  }
}
</script>

<script setup>
import RegistrationForm from './components/RegistrationForm.vue'
import NavBart from './components/NavBart.vue';
</script>

<template>
  <header>
    <NavBart />
  </header>

  <main>
      <RegistrationForm/>
  </main>
</template>



<style>
main{
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>

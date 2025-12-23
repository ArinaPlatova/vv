<template>
  <div class="home-page">
    <header class="header">
      <h1 class="title">Калькулятор подсетей</h1>
      <p class="subtitle">Расчет адреса сети, количества доступных хостов и широковещательного адреса</p>
    </header>
    
    <main class="main-content">
      <SubnetCalculator />
    </main>
    
    <footer class="footer">
      <p>Используйте калькулятор для расчета параметров подсетей IPv4</p>
    </footer>
  </div>
</template>

<script setup lang="ts">
import SubnetCalculator from '../components/SubnetCalculator.vue'
</script>
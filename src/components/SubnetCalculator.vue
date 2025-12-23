<template>
  <div class="calculator">
    <h2 class="calculator-title">Калькулятор подсетей</h2>
    
    <div class="calculator-inputs">
      <div class="input-group">
        <label for="ip-address" class="input-label">IP адрес:</label>
        <input
          id="ip-address"
          v-model="ipAddress"
          type="text"
          placeholder="192.168.1.150"
          class="input-field"
          :class="{ 'input-error': !isValidIp && ipAddress.length > 0 }"
          @keyup.enter="calculate"
          @input="validateIp"
        />
        <div v-if="!isValidIp && ipAddress.length > 0" class="error-message">
          Введите корректный IP адрес (0-255.0-255.0-255.0-255)
        </div>
      </div>

      <div class="input-group">
        <label for="subnet-mask" class="input-label">Маска подсети:</label>
        <select
          id="subnet-mask"
          v-model="selectedMask"
          class="select-field"
        >
          <option v-for="mask in SUBNET_MASKS" :key="mask.value" :value="mask.value">
            {{ mask.value }} {{ mask.cidr }} (доступно хостов: {{ mask.availableHosts }})
          </option>
        </select>
      </div>

      <button
        class="calculate-button"
        :disabled="!isValidIp || !selectedMask"
        @click="calculate"
      >
        Рассчитать
      </button>
    </div>

    <div v-if="showResults" class="results-container">
      <h3 class="results-title">Результаты расчета:</h3>
      
      <div class="results-grid">
        <div class="result-item">
          <span class="result-label">Введенный IP:</span>
          <span class="result-value">{{ ipAddress }}</span>
        </div>
        
        <div class="result-item">
          <span class="result-label">Выбранная маска:</span>
          <span class="result-value">{{ selectedMask }}</span>
        </div>
        
        <div class="result-item">
          <span class="result-label">Адрес сети:</span>
          <span class="result-value highlight">{{ networkAddress }}</span>
        </div>
        
        <div class="result-item">
          <span class="result-label">Количество возможных адресов:</span>
          <span class="result-value highlight">{{ addressesCount }}</span>
        </div>
        
        <div v-if="broadcastAddress" class="result-item">
          <span class="result-label">Широковещательный адрес:</span>
          <span class="result-value">{{ broadcastAddress }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import { SUBNET_MASKS } from '../constants/masks'
import { isIpValid } from '../utils/ipValidation'
import { getNetworkAddress, getAddressesCount, getBroadcastAddress } from '../utils/networkCalculations'

const ipAddress = ref('192.168.1.150')
const selectedMask = ref('255.255.255.0')
const showResults = ref(false)
const networkAddress = ref('')
const addressesCount = ref(0)
const broadcastAddress = ref('')

const isValidIp = computed(() => isIpValid(ipAddress.value))

const validateIp = () => {
  // Автоматически добавляем точки
  if (ipAddress.value.length > 0 && !ipAddress.value.includes('.') && ipAddress.value.length >= 3) {
    const cleanValue = ipAddress.value.replace(/[^0-9]/g, '')
    if (cleanValue.length > 3) {
      ipAddress.value = cleanValue.replace(/(\d{3})(\d+)/, '$1.$2')
    }
  }
}

const calculate = () => {
  if (!isValidIp.value || !selectedMask.value) return
  
  networkAddress.value = getNetworkAddress(ipAddress.value, selectedMask.value)
  addressesCount.value = getAddressesCount(selectedMask.value)
  broadcastAddress.value = getBroadcastAddress(networkAddress.value, selectedMask.value)
  showResults.value = true
}

onMounted(() => {
  calculate()
})
</script>
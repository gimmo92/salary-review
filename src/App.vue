<script setup>
import { ref, watch } from 'vue'
import SalaryReview from './components/SalaryReview.vue'

const COMPANY_KEY = 'salary-review:company-name'
const companyName = ref(localStorage.getItem(COMPANY_KEY) || '')
watch(companyName, (value) => localStorage.setItem(COMPANY_KEY, value))
</script>

<template>
  <div class="app-shell">
    <header class="app-header">
      <h1>Salary review</h1>
      <label class="company">
        <span>Azienda</span>
        <input v-model="companyName" type="text" placeholder="Nome azienda (per le lettere)" />
      </label>
    </header>
    <main class="app-main">
      <SalaryReview :company-name="companyName" />
    </main>
  </div>
</template>

<style scoped>
.app-shell { min-height: 100vh; display: flex; flex-direction: column; background: #eef2f6; }
.app-header {
  display: flex; align-items: center; justify-content: space-between; gap: 16px;
  padding: 14px 24px; background: #fff; border-bottom: 1px solid var(--border);
}
.app-header h1 { font-size: 18px; font-weight: 700; }
.company { display: flex; align-items: center; gap: 8px; font-size: 13px; color: var(--text-secondary); }
.company input {
  font: inherit; color: var(--text-primary); padding: 6px 10px; min-width: 240px;
  border: 1px solid var(--border); border-radius: var(--radius-sm);
}
.app-main { flex: 1; padding: 16px; }
</style>

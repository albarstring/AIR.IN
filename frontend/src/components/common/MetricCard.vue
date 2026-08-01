<template>
  <Card class="relative p-5 transition-all duration-200 hover:-translate-y-0.5">
    <div class="flex items-center gap-4">
      <!-- Icon container on the left (matching layout of other cards) -->
      <div class="flex h-12 w-12 shrink-0 items-center justify-center rounded-xl border border-slate-200 text-emerald-600 bg-white">
        <component :is="icon" class="h-6 w-6" />
      </div>

      <div class="space-y-0.5">
        <p class="text-[10px] sm:text-xs font-semibold text-slate-400 uppercase tracking-wider leading-none mb-1">{{ title }}</p>
        <h3 class="text-xl sm:text-2xl font-black tracking-tight text-slate-900 leading-tight">{{ value }}</h3>
        <p v-if="subtext" class="text-[11px] sm:text-xs font-medium text-slate-500 mt-1 leading-none">{{ subtext }}</p>
      </div>
    </div>
  </Card>
</template>

<script setup lang="ts">
import { type Component } from 'vue'
import Card from '../ui/Card.vue'

interface Props {
  title: string
  value: string | number
  subtext?: string
  icon: Component
  variant?: 'emerald' | 'sky' | 'amber' | 'rose' | 'indigo'
}

withDefaults(defineProps<Props>(), {
  variant: 'emerald',
})
</script>

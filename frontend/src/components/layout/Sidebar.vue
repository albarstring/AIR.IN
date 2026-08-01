<template>
  <aside class="w-64 border-r border-slate-200/80 bg-white flex flex-col justify-between h-screen sticky top-0 z-30 select-none">
    <div class="flex flex-col">
      <!-- Top Brand Logo -->
      <div class="p-6 border-b border-slate-100 flex items-center gap-3">
        <div class="flex h-10 w-10 items-center justify-center rounded-xl bg-emerald-600 text-white">
          <Droplets class="h-6 w-6" />
        </div>
        <div>
          <h2 class="text-xl font-extrabold tracking-tight text-emerald-600 leading-tight">air.in</h2>
          <p class="text-[11px] font-medium text-slate-500">Smart Agriculture Platform</p>
        </div>
      </div>

      <!-- Navigation List -->
      <nav class="px-4 py-6 space-y-1.5">
        <router-link
          v-for="item in navItems"
          :key="item.to"
          :to="item.to"
          class="flex items-center gap-3 rounded-xl px-4 py-3 text-sm font-semibold transition-all"
          :class="[route.path === item.to ? 'bg-emerald-700 text-white' : 'text-slate-600 hover:bg-slate-50 hover:text-emerald-700']"
        >
          <component :is="item.icon" class="h-5 w-5" />
          <span>{{ item.label }}</span>
        </router-link>
      </nav>
    </div>
  </aside>
</template>

<script setup lang="ts">
import { useRoute } from 'vue-router'
import { Droplets, LayoutDashboard, MapPin, Scale, Calendar } from '@lucide/vue'

const route = useRoute()

const navItems = [
  { to: '/dashboard', label: 'Dashboard', icon: LayoutDashboard },
  { to: '/fields', label: 'Manajemen Lahan', icon: MapPin },
  { to: '/allocation', label: 'Alokasi Air', icon: Scale },
  { to: '/schedule', label: 'Jadwal Irigasi', icon: Calendar },
]
</script>

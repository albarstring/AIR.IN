<template>
  <div class="min-h-screen bg-slate-50 text-slate-900 font-sans antialiased flex flex-col justify-between selection:bg-emerald-500 selection:text-white">
    <div class="flex-grow flex flex-col md:flex-row">
      <!-- Sidebar (Desktop, hidden on mobile) -->
      <Sidebar v-if="!isLandingPage" class="hidden md:flex" />

      <!-- Mobile Header Bar (Mobile only) -->
      <div v-if="!isLandingPage" class="md:hidden border-b border-slate-200 bg-white px-4 py-3 flex items-center justify-between sticky top-0 z-40">
        <div class="flex items-center gap-2">
          <div class="flex h-9 w-9 items-center justify-center rounded-lg bg-emerald-600 text-white">
            <Droplets class="h-5.5 w-5.5" />
          </div>
          <div>
            <span class="text-base font-bold text-emerald-600">air.in</span>
          </div>
        </div>
        
        <!-- Toggle button -->
        <button @click="isMobileMenuOpen = !isMobileMenuOpen" class="p-1 rounded-lg border border-slate-200 text-slate-600 hover:bg-slate-50">
          <Menu v-if="!isMobileMenuOpen" class="h-5 w-5" />
          <X v-else class="h-5 w-5" />
        </button>
      </div>

      <!-- Mobile Menu Dropdown -->
      <div v-if="!isLandingPage && isMobileMenuOpen" class="md:hidden border-b border-slate-200 bg-white/95 backdrop-blur-md px-4 py-3 space-y-1.5 sticky top-15 z-30">
        <router-link
          v-for="item in navItems"
          :key="item.to"
          :to="item.to"
          @click="isMobileMenuOpen = false"
          class="flex items-center gap-3 rounded-xl px-4 py-3 text-sm font-semibold transition-all"
          :class="[route.path === item.to ? 'bg-emerald-700 text-white' : 'text-slate-600 hover:bg-slate-50 hover:text-emerald-700']"
        >
          <component :is="item.icon" class="h-5 w-5" />
          <span>{{ item.label }}</span>
        </router-link>

        <!-- Weather info inside mobile menu -->
        <div class="mt-4 pt-3 border-t border-slate-100 flex flex-col gap-2 text-xs text-slate-500">
          <div class="flex items-center gap-2">
            <Sun v-if="fieldStore.weather.weatherCode <= 1" class="h-4 w-4 text-amber-500" />
            <CloudRain v-else-if="fieldStore.weather.rain > 0" class="h-4 w-4 text-sky-500" />
            <Cloud v-else class="h-4 w-4 text-slate-400" />
            <span>{{ fieldStore.weather.weatherDesc }} ({{ fieldStore.weather.temperature }}°C)</span>
          </div>
          <div>El Niño Status: Level {{ fieldStore.elNinoSeverity }} / 10</div>
        </div>
      </div>

      <!-- Main Content Container -->
      <div class="flex-1 flex flex-col min-w-0">
        <!-- Top Bar / Header (Desktop only) -->
        <header v-if="!isLandingPage" class="hidden md:flex h-20 border-b border-slate-200/80 bg-white items-center justify-between px-8 sticky top-0 z-20">
          <!-- Left: Page Title & Subtitle (Removed air.in breadcrumb, improved typography) -->
          <div>
            <h1 class="text-xl font-bold text-slate-900 tracking-tight">{{ currentRouteName }}</h1>
            <p class="text-[11px] font-medium text-slate-400 mt-0.5">Smart Agriculture &amp; Water Allocation Platform</p>
          </div>

          <!-- Right: Quick Weather & Status -->
          <div class="flex items-center gap-4">
            <!-- Weather pill -->
            <div class="flex items-center gap-2.5 rounded-xl bg-slate-50 border border-slate-200/60 px-4 py-2 text-xs text-slate-700">
              <Sun v-if="fieldStore.weather.weatherCode <= 1" class="h-4.5 w-4.5 text-amber-500 animate-pulse" />
              <CloudRain v-else-if="fieldStore.weather.rain > 0" class="h-4.5 w-4.5 text-sky-500" />
              <Cloud v-else class="h-4.5 w-4.5 text-slate-400" />
              <div>
                <div class="flex items-center gap-1.5">
                  <span class="font-bold text-slate-800">{{ fieldStore.weather.weatherDesc }}</span>
                  <span class="text-slate-300">|</span>
                  <span class="font-black text-emerald-600">{{ fieldStore.weather.temperature }}°C</span>
                </div>
                <div class="text-[10px] text-slate-400 font-semibold mt-0.5">Evapotranspirasi ET0: {{ fieldStore.weather.et0 }} mm/d</div>
              </div>
            </div>

            <!-- El Nino pill -->
            <div class="flex items-center gap-2 rounded-xl bg-amber-50/70 border border-amber-200/80 px-4 py-2 text-xs text-amber-800">
              <Flame class="h-4.5 w-4.5 text-amber-600" />
              <div>
                <span class="font-bold block uppercase tracking-wider text-[9px] text-amber-600">El Niño Status</span>
                <span class="font-black text-amber-900 text-[11px] mt-0.5 block leading-none">Level {{ fieldStore.elNinoSeverity }} / 10</span>
              </div>
            </div>
          </div>
        </header>

        <main :class="[isLandingPage ? 'flex-grow' : 'p-4 md:p-8 flex-grow']">
          <router-view />
        </main>

        <!-- Footer -->
        <footer v-if="!isLandingPage" class="border-t border-slate-100 bg-white py-6">
          <div class="container mx-auto px-4 text-center text-xs text-slate-400 font-medium">
            <span>&copy; 2026 - Smart Agriculture &amp; Water Allocation System. All rights reserved.</span>
          </div>
        </footer>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, ref, onMounted } from 'vue'
import { useRoute } from 'vue-router'
import { useFieldStore } from './stores/fieldStore'
import Sidebar from './components/layout/Sidebar.vue'
import { Droplets, Menu, X, LayoutDashboard, MapPin, Scale, Calendar, Sun, Cloud, CloudRain, Flame } from '@lucide/vue'

const route = useRoute()
const fieldStore = useFieldStore()
const isLandingPage = computed(() => route.path === '/')
const isMobileMenuOpen = ref(false)

const navItems = [
  { to: '/dashboard', label: 'Dashboard', icon: LayoutDashboard },
  { to: '/fields', label: 'Manajemen Lahan', icon: MapPin },
  { to: '/allocation', label: 'Alokasi Air', icon: Scale },
  { to: '/schedule', label: 'Jadwal Irigasi', icon: Calendar },
]

const currentRouteName = computed(() => {
  if (route.path === '/dashboard') return 'Dashboard'
  if (route.path === '/fields') return 'Manajemen Lahan'
  if (route.path === '/allocation') return 'Alokasi Air'
  if (route.path === '/schedule') return 'Jadwal Irigasi'
  return route.name || ''
})

onMounted(() => {
  // Force light mode theme
  document.documentElement.classList.remove('dark')
})
</script>


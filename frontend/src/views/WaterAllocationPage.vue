<template>
  <div class="space-y-6 pb-12">
    <!-- Header Banner (White & Simple theme) -->
    <div class="relative overflow-hidden rounded-2xl border border-slate-200/80 bg-white p-6 md:p-8 text-slate-800">
      <div class="relative z-10 max-w-7xl">
        <div class="inline-flex items-center gap-2 rounded-full bg-emerald-50 border border-emerald-100 px-3 py-1 text-xs font-semibold text-emerald-700">
          <Scale class="h-3.5 w-3.5 text-emerald-600" />
          <span>Sistem Cerdas Pengelolaan Irigasi Lahan Pertanian</span>
        </div>
        <div class="flex flex-col md:flex-row md:items-center justify-between gap-6 mt-4">
          <div class="space-y-2 max-w-2xl">
            <h1 class="text-2xl md:text-3xl font-extrabold tracking-tight text-slate-900">Alokasi Air Irigasi &amp; Ranking Prioritas</h1>
            <p class="text-sm md:text-base text-slate-505 text-slate-500 leading-relaxed">
              Distribusi kuota air secara adil dan transparan berdasarkan skor kelangkaan, jenis tanah, &amp; El Niño simulator secara real-time.
            </p>
          </div>

          <Button variant="agri" @click="shareToWhatsApp" class="shrink-0">
            <MessageSquare class="h-4.5 w-4.5 mr-2" />
            Kirim Rekomendasi via WhatsApp
          </Button>
        </div>
      </div>
    </div>

    <!-- Simulator & Supply Config Cards -->
    <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
      <!-- El Nino Simulator Slider -->
      <Card class="p-5 border-sky-200/80 bg-sky-50/40">
        <div class="flex items-center justify-between mb-3">
          <div class="flex items-center gap-2">
            <Flame class="h-5 w-5 text-sky-600" />
            <h3 class="font-bold text-slate-900">Simulator Keparahan El Niño</h3>
          </div>
          <Badge variant="secondary">Level {{ fieldStore.elNinoSeverity }} / 10</Badge>
        </div>
        <p class="text-xs text-slate-500 mb-4">Makin tinggi level, makin tinggi faktor penalti kelangkaan air tanah.</p>

        <input
          type="range"
          min="1"
          max="10"
          step="1"
          :value="fieldStore.elNinoSeverity"
          @input="(e: any) => fieldStore.setElNinoSeverity(Number(e.target.value))"
          class="w-full h-2.5 bg-sky-200 rounded-lg appearance-none cursor-pointer accent-sky-600"
        />
        <div class="flex justify-between text-[11px] font-semibold text-slate-500 mt-2">
          <span>Level 1 (Normal)</span>
          <span>Level 5 (Ancaman Sedang)</span>
          <span>Level 10 (Kekeringan Parah)</span>
        </div>
      </Card>

      <!-- Total Available Water Supply -->
      <Card class="p-5 border-sky-200 bg-sky-50/40 dark:border-sky-900/60 dark:bg-sky-950/20">
        <div class="flex items-center justify-between mb-3">
          <div class="flex items-center gap-2">
            <Droplets class="h-5 w-5 text-sky-600" />
            <h3 class="font-bold text-slate-900 dark:text-slate-100">Total Pasokan Air Irigasi Tersedia</h3>
          </div>
          <Badge variant="secondary">{{ formatLiters(fieldStore.totalWaterSupply) }}</Badge>
        </div>
        <p class="text-xs text-slate-500 mb-4">Atur kapasitas volume total air bendungan/waduk yang akan didistribusikan.</p>

        <div class="flex items-center gap-3">
          <input
            type="number"
            step="10000"
            min="10000"
            :value="fieldStore.totalWaterSupply"
            @input="(e: any) => fieldStore.setTotalWaterSupply(Number(e.target.value))"
            class="flex-1 rounded-lg border border-slate-300 px-3 py-2 text-sm font-bold text-slate-900 focus:outline-none focus:ring-2 focus:ring-sky-500 dark:border-slate-700 dark:bg-slate-800 dark:text-slate-100"
          />
          <span class="text-xs font-semibold text-slate-500">Liter</span>
        </div>
      </Card>
    </div>

    <!-- Ranked Water Allocation Table -->
    <Card class="overflow-hidden">
      <div class="p-5 border-b border-slate-200/80 dark:border-slate-800 flex items-center justify-between">
        <div>
          <h3 class="font-bold text-slate-900 dark:text-slate-100">Daftar Prioritas &amp; Proporsi Alokasi Air</h3>
          <p class="text-xs text-slate-500">Urutan lahan dari skor risiko kekeringan tertinggi ke terendah</p>
        </div>
        <Badge variant="default">{{ fieldStore.fieldRisks.length }} Lahan Terurut</Badge>
      </div>

      <div class="overflow-x-auto">
        <table class="w-full text-left text-sm text-slate-600 dark:text-slate-400">
          <thead class="bg-slate-50 text-xs font-semibold uppercase text-slate-500 border-b border-slate-200 dark:bg-slate-900 dark:border-slate-800">
            <tr>
              <th class="px-5 py-3.5">Rank</th>
              <th class="px-5 py-3.5">Nama Lahan</th>
              <th class="px-5 py-3.5">Pemilik</th>
              <th class="px-5 py-3.5">Luas (Ha)</th>
              <th class="px-5 py-3.5">Skor Risiko</th>
              <th class="px-5 py-3.5">Tingkat Risiko</th>
              <th class="px-5 py-3.5">Rekomendasi Air (Liter)</th>
              <th class="px-5 py-3.5">Proporsi (%)</th>
            </tr>
          </thead>
          <tbody class="divide-y divide-slate-200/80 dark:divide-slate-800">
            <tr
              v-for="(fr, idx) in fieldStore.fieldRisks"
              :key="fr.fieldId"
              class="hover:bg-slate-50/80 dark:hover:bg-slate-900/50 transition-colors"
              :class="{ 'bg-rose-50/30 dark:bg-rose-950/20': fr.riskLevel === 'Kritis' }"
            >
              <td class="px-5 py-4 font-black text-slate-400">
                #{{ idx + 1 }}
              </td>
              <td class="px-5 py-4 font-bold text-slate-900 dark:text-slate-100">
                {{ fr.fieldName }}
              </td>
              <td class="px-5 py-4">{{ fr.owner }}</td>
              <td class="px-5 py-4 font-medium text-slate-800 dark:text-slate-200">{{ fr.areaHa }} Ha</td>
              <td class="px-5 py-4 font-extrabold text-slate-900 dark:text-slate-100">
                {{ fr.riskScore }} / 100
              </td>
              <td class="px-5 py-4">
                <Badge :variant="fr.riskLevel === 'Kritis' ? 'danger' : fr.riskLevel === 'Tinggi' ? 'warning' : 'default'">
                  {{ fr.riskLevel }}
                </Badge>
              </td>
              <td class="px-5 py-4 font-bold text-emerald-600 dark:text-emerald-400">
                {{ formatLiters(fr.recommendedWaterLiters) }}
              </td>
              <td class="px-5 py-4">
                <div class="flex items-center gap-2">
                  <div class="w-16 h-2 rounded-full bg-slate-200 overflow-hidden dark:bg-slate-800">
                    <div class="h-full bg-emerald-500 rounded-full" :style="{ width: fr.allocatedPercent + '%' }"></div>
                  </div>
                  <span class="text-xs font-semibold text-slate-700 dark:text-slate-300">{{ fr.allocatedPercent }}%</span>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </Card>
  </div>
</template>

<script setup lang="ts">
import { useFieldStore } from '../stores/fieldStore'
import { formatLiters } from '../lib/utils'
import Button from '../components/ui/Button.vue'
import Card from '../components/ui/Card.vue'
import Badge from '../components/ui/Badge.vue'
import { Flame, Droplets, MessageSquare, Scale } from '@lucide/vue'

const fieldStore = useFieldStore()

function shareToWhatsApp() {
  let text = `*HARVEY WATER ALLOCATION REPORT*\n`
  text += `📅 Tanggal: ${new Date().toLocaleDateString('id-ID')}\n`
  text += `🔥 Level El Niño: ${fieldStore.elNinoSeverity}/10\n`
  text += `💧 Total Pasokan Air: ${formatLiters(fieldStore.totalWaterSupply)}\n\n`
  text += `*DAFTAR PRIORITAS ALOKASI IRIGASI:*\n`

  fieldStore.fieldRisks.forEach((fr, idx) => {
    text += `${idx + 1}. *${fr.fieldName}* (${fr.owner})\n`
    text += `   - Risiko: ${fr.riskScore}/100 (${fr.riskLevel})\n`
    text += `   - Kuota Irigasi: ${formatLiters(fr.recommendedWaterLiters)} (${fr.allocatedPercent}%)\n\n`
  })

  text += `_Disusun oleh Sistem Cerdas Harvey_`

  const url = `https://wa.me/?text=${encodeURIComponent(text)}`
  window.open(url, '_blank')
}
</script>

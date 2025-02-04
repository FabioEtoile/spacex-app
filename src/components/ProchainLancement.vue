<script lang="ts">
import { defineComponent, ref, onMounted, onUnmounted } from 'vue'

interface LancementProchain {
  name: string;
  date_utc: string;
}

export default defineComponent({
  name: 'ProchainLancement',
  setup() {
    const lancementProchain = ref<LancementProchain | null>(null)
    const compteRebours = ref<number>(0)
    let timer: number | undefined

    function majCompteRebours() {
      if (!lancementProchain.value) return
      const dateLaunch = new Date(lancementProchain.value.date_utc).getTime()
      const now = Date.now()
      const diffSec = Math.floor((dateLaunch - now) / 1000)
      compteRebours.value = diffSec > 0 ? diffSec : 0
    }

    function formaterDate(dateStr: string) {
      const d = new Date(dateStr)
      return d.toLocaleDateString('fr-FR', { day: '2-digit', month: '2-digit', year: 'numeric' })
    }

    onMounted(async () => {
      const resp = await fetch('https://api.spacexdata.com/v5/launches/next')
      const data: LancementProchain = await resp.json()
      lancementProchain.value = data

      majCompteRebours()
      timer = window.setInterval(majCompteRebours, 1000)
    })

    onUnmounted(() => {
      if (timer) clearInterval(timer)
    })

    return {
      lancementProchain,
      compteRebours,
      formaterDate
    }
  }
})
</script>

<template>
  <div class="p-4 bg-gray-100 rounded-md text-center">
    <h2 class="text-2xl font-bold mb-2">Prochain lancement</h2>

    <div v-if="lancementProchain">
      <p class="text-xl font-semibold">{{ lancementProchain.name }}</p>
      <p>Date : {{ formaterDate(lancementProchain.date_utc) }}</p>
      <p class="text-lg font-semibold mt-2">
        Compte à rebours : {{ compteRebours }} secondes
      </p>
    </div>
    <div v-else>
      <p>Chargement en cours...</p>
      <div class="bg-black/50 backdrop-blur-md rounded-lg p-6 shadow-lg">
    </div>
  </div>
</div>
</template>

<style scoped></style>

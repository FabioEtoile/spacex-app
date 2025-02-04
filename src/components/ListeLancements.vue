<script lang="ts">
import { defineComponent, ref, computed, onMounted } from 'vue'
import ModalLancement from './ModalLancement.vue'

interface Lancement {
  id: string;
  name: string;
  date_utc: string;
  success?: boolean;
  links: {
    patch?: {
      small?: string;
    };
    article?: string;
    webcast?: string;
  };
}

export default defineComponent({
  name: 'ListeLancements',
  components: {
    ModalLancement,
  },
  setup() {
    const lancementsBruts = ref<Lancement[]>([])
    const filtre = ref<string>('tous')
    const lancementSelectionne = ref<Lancement | null>(null)

    onMounted(async () => {
      const reponse = await fetch('https://api.spacexdata.com/v5/launches/past')
      let data: Lancement[] = await reponse.json()


      data.sort((a, b) => new Date(b.date_utc).getTime() - new Date(a.date_utc).getTime())


      let foundStarlink = false
      const filtered: Lancement[] = []
      for (const launch of data) {
        if (launch.name.includes('Starlink')) {
          if (!foundStarlink) {
            filtered.push(launch)
            foundStarlink = true
          }
        } else {
          filtered.push(launch)
        }
      }

      lancementsBruts.value = filtered
    })

    const lancementsAffiches = computed(() => {
      let result = lancementsBruts.value
      if (filtre.value === 'success') {
        result = result.filter(l => l.success === true)
      } else if (filtre.value === 'fail') {
        result = result.filter(l => l.success === false)
      }
      return result.slice(0, 10)
    })

    function formaterDate(dateStr: string) {
      const d = new Date(dateStr)
      return d.toLocaleDateString('fr-FR', { day: '2-digit', month: '2-digit', year: 'numeric' })
    }

    function selectionnerLancement(lancement: Lancement) {
      lancementSelectionne.value = lancement
    }

    return {
      filtre,
      lancementSelectionne,
      lancementsAffiches,
      formaterDate,
      selectionnerLancement,
    }
  },
})
</script>

<template>
  <div class="flex justify-center items-start space-x-12 max-w-6xl mx-auto ml-16">
    <!-- Liste des lancements à gauche -->
    <div class="w-1/2">
      <h2 class="text-2xl font-bold mb-4 text-left">Derniers lancements</h2>

      <!-- Sélect -->
    <div class="mb-4 flex items-center space-x-2">
    <label class="text-lg font-semibold text-white"> Filtrer :&nbsp;</label>
    <select id="filtreSelect" v-model="filtre" 
      class="border border-gray-600 bg-gray-900 text-white text-lg px-4 py-2 rounded-lg shadow-md cursor-pointer transition duration-200 ease-in-out hover:bg-gray-700 focus:ring-2 focus:ring-blue-500">
      <option value="tous">🌍 Tous</option>
      <option value="success">✅ Réussis</option>
      <option value="fail">❌ Échoués</option>
  </select>
</div>



      <!-- Liste des 10 derniers lancements --> 
      <div class="space-y-4">
        <div
          v-for="lancement in lancementsAffiches"
          :key="lancement.id"
          @click="selectionnerLancement(lancement)"
          class="p-4 bg-gray-800 text-white rounded-lg shadow-lg hover:bg-gray-700 transition cursor-pointer"
        >
        <h3 class="text-lg font-semibold">🚀 {{ lancement.name }}</h3>
          <p class="text-gray-300 text-sm">{{ formaterDate(lancement.date_utc) }}</p>
          <div v-if="lancement.success !== undefined" class="mt-2 ml-2"> 
            <span v-if="lancement.success" class="text-green-400 font-bold">✔️ Réussi</span>
            <span v-else class="text-red-400 font-bold">❌ Échec</span>
          </div>
        </div>
      </div>
    </div>

    <!-- Détails du lancement à droite -->
    <div class="w-1/2">
      <ModalLancement
        v-if="lancementSelectionne"
        :lancement="lancementSelectionne"
        @close="lancementSelectionne = null"
      />
    </div>
  </div>
</template>

<style scoped>
div {
  margin-left: 2rem;
}
</style>

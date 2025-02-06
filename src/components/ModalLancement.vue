<script setup lang="ts">
import { ref, computed, watch, onMounted } from 'vue'

interface Launchpad {
  name: string;
}
interface Payload {
  id: string;
  name: string;
  customers?: string[];
}
interface Lancement {
  id: string;
  name: string;
  date_utc: string;
  details?: string;
  success?: boolean;
  links: {
    patch?: {
      small?: string;
    };
    article?: string;
    webcast?: string;
  };
  launchpad?: string;
  payloads?: string[];
}
const props = defineProps<{ lancement: Lancement | null }>()
const emit = defineEmits(['close'])

const afficherVideo = ref(false)
const nomLaunchpad = ref<string>('')
const payloads = ref<Payload[]>([])
const details = ref<string>('')
const article = ref<string>('')

const idYoutube = computed(() => {
  const webcastUrl = props.lancement?.links?.webcast
  if (!webcastUrl) return null

  const shortMatch = webcastUrl.match(/youtu\.be\/([^?]+)/i)
  if (shortMatch && shortMatch[1]) {
    return shortMatch[1]
  }

  try {
    const urlObj = new URL(webcastUrl)
    return urlObj.searchParams.get('v')
  } catch {
    return null
  }
})

const imageMission = computed(() => props.lancement?.links?.patch?.small || '')

const formaterDate = (dateStr: string) => {
  const d = new Date(dateStr)
  return d.toLocaleDateString('fr-FR', { day: '2-digit', month: '2-digit', year: 'numeric' })
}

const fermerModal = () => emit('close')

const chargerDonnees = async () => {
  nomLaunchpad.value = ''
  payloads.value = []
  details.value = props.lancement?.details || 'Aucune information détaillée disponible.'
  article.value = props.lancement?.links?.article || ''

  if (props.lancement?.launchpad) {
    try {
      const repLaunchpad = await fetch(`https://api.spacexdata.com/v4/launchpads/${props.lancement.launchpad}`)
      const dataLp: Launchpad = await repLaunchpad.json()
      nomLaunchpad.value = dataLp.name
    } catch (err) {
      console.error('Erreur fetch launchpad :', err)
    }
  }

  if (props.lancement?.payloads && Array.isArray(props.lancement.payloads)) {
    for (const payloadId of props.lancement.payloads) {
      try {
        const repPayload = await fetch(`https://api.spacexdata.com/v4/payloads/${payloadId}`)
        const dataPl: Payload = await repPayload.json()
        payloads.value.push(dataPl)
      } catch (err) {
        console.error('Erreur fetch payload :', err)
      }
    }
  }
}

onMounted(chargerDonnees)
watch(() => props.lancement, chargerDonnees, { deep: true })
</script>

<template>
  <div class="fixed inset-0 bg-black/50 flex justify-end items-center z-50 p-4">
    <div class="relative bg-white p-8 rounded-lg shadow-lg max-w-4xl w-full mx-auto flex flex-col items-start text-left">
      <!-- Bouton de fermeture -->
      <button class="absolute top-4 right-4 text-gray-500 hover:text-gray-700 text-2xl" @click="fermerModal">
        ✕
      </button>

      <!-- Titre du lancement -->
      <h2 class="text-3xl font-bold mb-4 text-gray-900">🚀 {{ lancement?.name }}</h2>

      <!-- Date -->
      <p class="text-lg text-gray-700 mb-2">
        <strong>Date :</strong> {{ lancement ? formaterDate(lancement.date_utc) : "Inconnue" }}
      </p>

      <!-- Statut -->
      <div v-if="lancement?.success !== undefined" class="text-lg font-semibold mb-4">
        <span :class="lancement?.success ? 'text-green-500' : 'text-red-500'">
          {{ lancement?.success ? '✔️ Réussi' : '❌ Échec' }}
        </span>
      </div><br>

      <!-- Détails du lancement -->
      <div class="mb-4">
        <strong class="text-lg">📜 Détails :</strong>
        <p class="text-gray-700">{{ details }}</p>
      </div>

      <!-- Lien vers l'article -->
      <div v-if="article" class="mb-4">
        <a :href="article" target="_blank" class="text-blue-600 underline text-lg font-semibold">
          🔗 Lire l'article
        </a>
      </div>
      <p v-else class="mb-4 text-gray-500 text-lg">Aucun article disponible.</p><br>

      <!-- Image de la mission -->
      <div v-if="imageMission" class="mb-6 flex justify-start">
        <img :src="imageMission" alt="Mission image"
          class="max-h-48 object-contain rounded-lg shadow-md border border-gray-300 p-2" />
      </div>

      <!-- 📍 Lieu -->
      <div class="mb-6 text-lg flex items-center justify-start space-x-2">
        <span>📍</span>
        <p><strong>Lieu :</strong> {{ nomLaunchpad || "Lieu inconnu" }}</p>
      </div>

      <!-- 📦 Payloads -->
      <div v-if="payloads.length" class="mb-6 text-left">
        <strong class="block text-lg mb-2">📦 Payloads :</strong>
        <ul class="list-none space-y-2 text-gray-700">
          <li v-for="pl in payloads" :key="pl.id" class="bg-gray-100 p-2 rounded shadow-sm w-fit">
            {{ pl.name }} <span v-if="pl.customers?.length">(Clients : {{ pl.customers.join(', ') }})</span>
          </li>
        </ul>
      </div>

      <!-- 🎬 Checkbox pour afficher la vidéo -->
      <div class="mb-4">
        <label class="inline-flex items-center space-x-2 text-lg">
          <input type="checkbox" v-model="afficherVideo" class="h-5 w-5">
          <span>🎬 Afficher la vidéo YouTube</span>
        </label>
      </div>

      <!-- 🎥 Vidéo YouTube -->
      <div v-if="afficherVideo && idYoutube" class="mb-4 flex justify-start">
        <iframe width="560" height="315" :src="`https://www.youtube.com/embed/${idYoutube}`"
          frameborder="0" allowfullscreen class="rounded-lg shadow-md border border-gray-300">
        </iframe>
      </div>
    </div>
  </div>
</template>

<style scoped>
ul {
  text-align: center;
}
a {
  text-decoration: none; 
  color: #007bff; 
}

a:hover {
  color: #0056b3; 
}
</style>

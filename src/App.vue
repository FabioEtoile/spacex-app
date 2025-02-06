  <template>
  <div class="min-h-screen flex items-start">
    <div class="container mx-auto py-12 px-4 sm:px-6 lg:px-8 w-full">
      <div class="space-y-12 max-w-6xl mx-auto">
        <header class="text-center space-y-2">
          <h1 class="text-4xl sm:text-5xl font-bold text-black drop-shadow-lg">
            🚀 Suivi des Lancements SpaceX
          </h1>
        </header>

        <div class="space-y-8">
          <ProchainLancement />
          <ListeLancements />
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import ProchainLancement from './components/ProchainLancement.vue'
import ListeLancements from './components/ListeLancements.vue'
</script>


<style>
html, body {
  height: 100%;
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

#app {
  min-height: 100vh;
  
}
body {
  background-image: url('/src/assets/image.jpg');
  background-size: cover;
  background-position: center;
  background-attachment: fixed;
  background-repeat: no-repeat;
}
</style>



<template>
  <v-app id="app">
    <v-content>
			<Index />
    </v-content>
  </v-app>
</template>

<script>
import Index from '@/components/index.vue'

export default {
  components: {
    Index,
  },
}
</script>

<style>
	#app {
		background: #FAFAFA;
	}
</style>
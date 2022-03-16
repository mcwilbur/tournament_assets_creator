<template>
  <div class="home">
    <TournamentInfo/>
  </div>
</template>

<script>
// @ is an alias to /src
import TournamentInfo from '@/components/TournamentInfo.vue'

export default {
  name: 'HomeView',
  components: {
    TournamentInfo
  }
}
</script>

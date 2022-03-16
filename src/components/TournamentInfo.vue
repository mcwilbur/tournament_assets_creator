<template>
  <div id="tournamentInfo">
      <div id="tournamentInput" class="form-group">
          <label class="form-label">Tournament Name</label>
          <input class="form-control" type="text" v-model="TournamentName"/>
          <label class="form-label">Tournament date</label>
          <input class="form-control" type="datetime-local" v-model="TournamentDate"/>
          <label class="form-label"></label>
          <input class="form-control" type="text"  v-model="TournamentGame"/>
          <label class="form-label"></label>
          <input class="form-control" type="text" v-model="TournamentType"/>
          <label class="form-label"></label>
          <input class="form-control" type="text" v-model="TournamentStyle"/>          
      </div>
    <div id="card">
      <h1>{{ TournamentName }}</h1>
      <h1>{{ TournamentDate }}</h1>
      <h1>{{ TournamentType }}</h1>
      <h1>{{ TournamentStyle }}</h1>
    </div>
  </div>
</template>

<script>
export default {
  name: "TournamentInfo",
  data() {
    return {
      TournamentName: "LCh April 2022",
      TournamentDate: "2022-04-09T13:30",
      TournamentGame: "Pokémon",
      TournamentType: "League Challenge",
      TournamentStyle: "BO1 Swiss",
    };
  },
  methods: {
      pickDateTime()
      {
          
      }
  }
};
</script>

<style>
#tournamentInfo {
    margin: 15px;
}
</style>
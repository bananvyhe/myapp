<template>
  <div id="telpanel">
    <label>Team Name</label>
    <input type="text" v-model="team.name" />
    <h4>Players</h4>
    {{team}}<br><hr> 
    {{team.name}}<br><hr>
    {{team.players_attributes}}<br><hr>
     
  <div v-for="(player, index) in team.players_attributes">
    <div v-if="player._destroy == '1'">
      {{ player.name }} will be removed. <button v-on:click="undoRemove(index)">Undo</button>
    </div>
    <div v-else>
      <label>Player Name</label>
      {{ player.name }}
      <input type="text" v-model="player.name" />
      <button v-on:click="removePlayer(index)">Remove</button>
    </div>
    <hr />
  </div>
  <button v-on:click="addPlayer">Add Player</button>
  <br />
  <button v-on:click="saveTeam">Save Team</button>
  </div>
</template>

<script>
// setTimeout(function(){   
//       $('.tel').css('visibility', 'visible').addClass('slideUpReturn');
// }, 1300);
import axios from 'packs/axios.min.js'

 
console.log('telpanel');
var element = document.getElementById("team-form")

if (element != null) {
 
  console.log(element);
  var id = element.dataset.id
  var team = JSON.parse(element.dataset.team)

  var players_attributes = JSON.parse(element.dataset.playersAttributes)
  players_attributes.forEach(function(player) { player._destroy = null })
  team.players_attributes = players_attributes 
}
export default {
  name: 'telpanel', 
  data: function () {
    return {
      id: id, 
      team: team, 
      postBody: '',
      errors: []
    }
  },
  created() {
  	axios.get('/teams')
	  .then(function (response) {
	    console.log(response);
	  })
	  .catch(function (error) {
	    console.log(error);
	  });	
  },
  mounted() {
 
  },
  updated() {
     
  },
  methods: {
        addPlayer: function() {
          this.team.players_attributes.push({
            id: null,
            name: "",
            //position: "",
            _destroy: null
          })
          console.log(this.team.players_attributes)
          console.log(this.team)
        },
        removePlayer: function(index) {
          var player = this.team.players_attributes[index]
          if (player.id == null) {
            this.team.players_attributes.splice(index, 1)
          } else {
            this.team.players_attributes[index]._destroy = "1"
          }
          console.log(this.team.players_attributes)
          console.log(this.team)
        },
        undoRemove: function(index) {
          this.team.players_attributes[index]._destroy = null
        },
        saveTeam: function() {
          // Create a new team
          if (this.id == null) {
            axios.post('/teams', { team: this.team }).then(response => {
            Turbolinks.visit('/teams' ) 
            }, response => {
              console.log(response)
            })
          // Edit an existing team
          } else {
            axios.put(`/teams/${this.id}`, { team: this.team }).then(response => {
               Turbolinks.visit('/teams')
            }, response => {
              console.log(response)
            })
          }
        },
        existingTeam: function() {
          return this.team.id != null
        },
 
  }
  
}
</script>

<style scoped>
 
 
</style>
<template>
    <div>
      <form @submit.prevent="searchPokemon">
        <input type="text" v-model="pokemonName" placeholder="Enter Pokemon Name">
      </form>

      <div v-if="showDiv" class="pokemon-card">
        <div class="main-info">
            <h2>{{ pokemon.name }}</h2>
            <img :src="pokemon.sprites.front_default" alt="Pokemon image">
        </div>
        <div class="stats">
            <h3>Stats</h3>
            <p>HP: {{ pokemon.stats[5].base_stat }}</p>
            <p>Attack: {{ pokemon.stats[4].base_stat }}</p>
            <p>Defense: {{ pokemon.stats[3].base_stat }}</p>
            <p>Special Attack: {{ pokemon.stats[2].base_stat }}</p>
            <p>Special Defense: {{ pokemon.stats[1].base_stat }}</p>
            <p>Speed: {{ pokemon.stats[0].base_stat }}</p>
        </div>
        <div class="evolution-chain">
            <h3>Evolution Chain</h3>
            <ul>
                <li v-for="evolution in evolutionChain" :key="evolution.id">{{ evolution.name }}</li>
            </ul>
        </div>
      </div>
  
      <div v-if="pokemonList.length">
        <h2>All Pokemons</h2>
        <ul  class="pokemon-list">
          <li v-for="pokemon in filteredPokemonList" :key="pokemon.name">
            <a @click="showPokemon(pokemon.name)">{{ pokemon.name }}</a>
          </li>
        </ul>
      </div>

    </div>
  </template>
  
  <script lang="js">
  import axios from 'axios';
  
  export default {
    data() {
      return {
        pokemonName: '',
        pokemonList: [],
        pokemon: null,
        evolutionChain: [],
        showDiv: false
      };
    },
    computed: {
      filteredPokemonList() {
        this.showDiv = false;
        return this.pokemonList.filter(pokemon => pokemon.name.toLowerCase().includes(this.pokemonName.toLowerCase()));
      }
    },
    created() {
      this.fetchPokemonList();
    },
    methods: {
      async fetchPokemonList() {
        try {
          const response = await axios.get('https://pokeapi.co/api/v2/pokemon?limit=1000');
          this.pokemonList = response.data.results;
        } catch (error) {
          console.error(error);
        }
      },
      async showPokemon(name) {
        try {
          const response = await axios.get(`https://pokeapi.co/api/v2/pokemon/${name}`);
          this.pokemon = response.data;
          const evolutionChainResponse = await axios.get(`https://pokeapi.co/api/v2/evolution-chain/${this.pokemon.id}/`);
          this.evolutionChain = evolutionChainResponse.data.evolution_chain;
          this.showDiv = true;
        } catch (error) {
            console.error(error);
            }
        }
    }
    };
    </script>
  
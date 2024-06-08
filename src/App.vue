<template>
  <div :class="['pokedex-container', primaryTypeClass]">
    <!-- Contenedor de búsqueda -->
    <div class="search-container">
      <!-- Campo de entrada para el ID del Pokémon -->
      <input v-model="pokemonId" type="number" min="1" placeholder="Pokemon ID" class="search-input" />
      <!-- Botón para buscar el Pokémon -->
      <button @click="fetchPokemon" class="search-button">Buscar</button>
    </div>
    <!-- Sección de información del Pokémon -->
    <div v-if="pokemon.name" class="pokemon-info">
      <!-- Encabezado con el número de identificación y el nombre del Pokémon -->
      <div class="header">
        <h1>#{{ pokemon.id }} {{ capitalize(pokemon.name) }}</h1>
        <!-- Imagen del Pokémon -->
        <img :src="imgPokemon" :alt="pokemon.name" class="pokemon-image" />
      </div>
      <!-- Detalles del Pokémon -->
      <div class="pokemon-details">
        <!-- Información básica del Pokémon (altura, peso, tipo) -->
        <div class="info">
          <p><strong>Altura:</strong> {{ pokemon.height / 10 }} m</p>
          <p><strong>Peso:</strong> {{ pokemon.weight / 10 }} kg</p>
          <p><strong>Tipo:</strong>
            <!-- Iteración sobre los tipos del Pokémon -->
            <span v-for="type in pokemon.types" :key="type.slot" :class="['type', type.type.name]">
              {{ capitalize(type.type.name) }}
            </span>
          </p>
        </div>
        <!-- Estadísticas del Pokémon -->
        <div class="stats">
          <h3>Estadísticas</h3>
          <ul>
            <!-- Iteración sobre las estadísticas del Pokémon -->
            <li v-for="stat in pokemon.stats" :key="stat.stat.name" class="stat-item">
              <strong>{{ capitalize(stat.stat.name) }}:</strong> {{ stat.base_stat }}
              <q-linear-progress
                :value="stat.base_stat / 255"
                color="yellow"
                track-color="grey-3"
                style="width: 100%; margin-top: 5px;"
              />
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import axios from 'axios';

// Variables reactivas para almacenar los datos del Pokémon y la imagen
const pokemon = ref({});
const imgPokemon = ref('');
// Variable reactiva para almacenar el ID del Pokémon ingresado por el usuario
const pokemonId = ref("");

// Función para obtener los datos del Pokémon desde la API
async function fetchPokemon() {
  try {
    // Realizar solicitud HTTP GET a la API de Pokémon
    const response = await axios.get(`https://pokeapi.co/api/v2/pokemon/${pokemonId.value}`);
    // Asignar los datos del Pokémon y la URL de la imagen a las variables reactivas
    pokemon.value = response.data;
    imgPokemon.value = response.data.sprites.other['official-artwork'].front_default;

  } catch (error) {
    // Manejar errores en caso de que la solicitud falle
    console.error("Error fetching Pokemon data:", error);
  }
}

// Función para capitalizar la primera letra de una cadena
function capitalize(str) {
  return str.charAt(0).toUpperCase() + str.slice(1);
}

// Computed property to get the primary type class
const primaryTypeClass = computed(() => {
  if (pokemon.value.types && pokemon.value.types.length > 0) {
    return pokemon.value.types[0].type.name;
  }
  return '';
});
</script>

<style>
/* Estilos generales */
body {
  background-image: url('fondo.jpg'); /* Ruta de la imagen de fondo */
  background-size: cover;
  background-position: center;
  background-attachment: fixed;
  margin: 0;
  padding: 0;
  font-family: 'Arial', sans-serif;
}

.pokedex-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
  border-radius: 15px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  max-width: 800px;
  margin: 20px auto;
  background-color: rgba(255, 255, 255, 0.9); /* Fondo blanco semi-transparente */
  position: relative;
  z-index: 1;
}

.search-container {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
  width: 100%;
  justify-content: center;
}

.search-input {
  width: 150px;
  padding: 10px;
  border: 2px solid #aaa; /* Color del borde más oscuro */
  border-radius: 25px;
  margin-right: 10px;
  font-size: 16px;
}

.search-button {
  padding: 10px 20px;
  background-color: #ffcb05;
  border: none;
  border-radius: 25px;
  cursor: pointer;
  font-size: 16px;
  transition: background-color 0.3s;
}

.search-button:hover {
  background-color: #ffb300;
}

.pokemon-info {
  padding: 20px;
  border-radius: 15px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  width: 100%;
  position: relative;
  z-index: 1;
}

.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  border-bottom: 2px solid #aaa; /* Color del borde más oscuro */
  padding-bottom: 10px;
  margin-bottom: 10px;
}

.header h1 {
  font-size: 28px;
  color: #111; /* Letra más oscura */
}

.pokemon-image {
  width: 150px;
  height: 150px;
  border-radius: 50%;
  border: 2px solid #ffcb05;
}

.pokemon-details {
  display: flex;
  flex-direction: column;
}

.info {
  margin-bottom: 20px;
}

.info p {
  font-size: 18px;
  color: #111; /* Letra más oscura */
  margin: 5px 0;
}

.type {
  display: inline-block;
  padding: 5px 10px;
  border-radius: 15px;
  margin-right: 5px;
  font-size: 14px;
  color: #fff;
}

.stats {
  margin-top: 10px;
}

.stats h3 {
  font-size: 24px;
  color: #111; /* Letra más oscura */
  margin-bottom: 10px;
}

.stat-item {
  font-size: 18px;
  color: #111; /* Letra más oscura */
  margin: 5px 0;
}

.q-linear-progress {
  height: 12px;
  border-radius: 6px;
  overflow: hidden;
}

.q-linear-progress .q-linear-progress__track {
  background-color: #aaa; /* Color del track más oscuro */
}

.q-linear-progress .q-linear-progress__model {
  background-color: #4b48f3;
}

/* Colores por tipo de Pokémon para el fondo */
.pokedex-container.normal {
  background-color: rgba(168, 167, 122, 0.8); /* Transparente para ver la imagen de fondo */
}

.pokedex-container.fire {
  background-color: rgba(238, 129, 48, 0.8);
}

.pokedex-container.water {
  background-color: rgba(99, 144, 240, 0.8);
}

.pokedex-container.electric {
  background-color: rgba(247, 208, 44, 0.8);
}

.pokedex-container.grass {
  background-color: rgba(122, 199, 76, 0.8);
}

.pokedex-container.ice {
  background-color: rgba(150, 217, 214, 0.8);
}

.pokedex-container.fighting {
  background-color: rgba(194, 46, 40, 0.8);
}

.pokedex-container.poison {
  background-color: rgba(163, 62, 161, 0.8);
}

.pokedex-container.ground {
  background-color: rgba(226, 191, 101, 0.8);
}

.pokedex-container.flying {
  background-color: rgba(169, 143, 243, 0.8);
}

.pokedex-container.psychic {
  background-color: rgba(249, 85, 135, 0.8);
}

.pokedex-container.bug {
  background-color: rgba(166, 185, 26, 0.8);
}

.pokedex-container.rock {
  background-color: rgba(182, 161, 54, 0.8);
}

.pokedex-container.ghost {
  background-color: rgba(115, 87, 151, 0.8);
}

.pokedex-container.dragon {
  background-color: rgba(111, 53, 252, 0.8);
}

.pokedex-container.dark {
  background-color: rgba(112, 87, 70, 0.8);
}

.pokedex-container.steel {
  background-color: rgba(183, 183, 206, 0.8);
}

.pokedex-container.fairy {
  background-color: rgba(214, 133, 173, 0.8);
}

/* Colores por tipo de Pokémon para las etiquetas */
.type.normal {
  background-color: #A8A77A;
}

.type.fire {
  background-color: #EE8130;
}

.type.water {
  background-color: #6390F0;
}

.type.electric {
  background-color: #F7D02C;
}

.type.grass {
  background-color: #7AC74C;
}

.type.ice {
  background-color: #96D9D6;
}

.type.fighting {
  background-color: #C22E28;
}

.type.poison {
  background-color: #A33EA1;
}

.type.ground {
  background-color: #E2BF65;
}

.type.flying {
  background-color: #A98FF3;
}

.type.psychic {
  background-color: #F95587;
}

.type.bug {
  background-color: #A6B91A;
}

.type.rock {
  background-color: #B6A136;
}

.type.ghost {
  background-color: #735797;
}

.type.dragon {
  background-color: #6F35FC;
}

.type.dark {
  background-color: #705746;
}

.type.steel {
  background-color: #B7B7CE;
}

.type.fairy {
  background-color: #D685AD;
}
</style>

      <template>
        <div :style="{ backgroundColor: backgroundColor, padding: '20px', borderRadius: '10px'}">
          <div id="input">
            <q-input  v-model="pokemon" label="Ingresa el nombre o el numero del pokemon" stack-label :dense="dense" />
          </div>
          
          <button id="button" @click="traer()">Buscar Pokemon</button>
          <div id="block">
      
            <div id="block1">
              <div id="block1_1">
                <h3 id="type" v-for="(tipo, index) in capitalizedTipos" :key="index" :style="{ backgroundColor: typeColors[tipo.toLowerCase()], borderRadius:'0.3rem', border: '0.2rem solid white' }"> {{ tipo }}</h3>
                <h1 id="name">{{ capitalizedName }}</h1>
                <div id="block1_1_1">
                  <div>
                    <h3 id="height" v-show="altura"> {{ "Altura: " + altura }}</h3>
                  </div>
                  <div>
                    <h3 id="weight" v-show="peso"> {{ "Peso: " + peso }}</h3>
                  </div>
                </div>
              </div>
      
              <div id="block1_2">
                <img id="picture" :src="imgPokemon" alt="">
                <h3 id="number" v-show="number">{{ "# " + number }}</h3>
              </div>
      
            </div>
            
            <div id="block2">
              <div id="block2_1" v-show="imgPokemon2">
                <img id="picture2" :src="imgPokemon2" alt="">
              </div>
              <div id="block2_2">
                <div id="stats" v-for="(stat, index) in capitalizedEstadisticas" :key="index" style="margin-bottom: 20px;">
                  <h3 id="statsH3">{{ stat.nombre }}: {{ stat.cantidad }}</h3>
                  <q-circular-progress rounded
                    :value="stat.cantidad / 200 * 100"
                    size="90px"
                    :thickness="0.2"
                    color="#242429"
                    center-color="grey-8"
                    track-color="transparent"
                    class="q-ma-md"
                  />
                </div>
              </div>
            </div>
          </div>
        </div>
      </template>
      
      <script setup>
      import axios from "axios";
      import { ref, computed, watch } from "vue";
      
      let imgPokemon = ref("");
      let imgPokemon2 = ref("");
      let pokemon = ref("");
      let name = ref("");
      let number = ref("");
      let altura = ref("");
      let peso = ref("");
      let tipos = ref([]);
      let estadisticas = ref([]);
      let backgroundColor = ref("");
      
      // Define colors for each Pokémon type
      const typeColors = {
        normal: "#A8A77A",
        fire: "#EE8130",
        water: "#6390F0",
        electric: "#F7D02C",
        grass: "#7AC74C",
        ice: "#96D9D6",
        fighting: "#C22E28",
        poison: "#A33EA1",
        ground: "#E2BF65",
        flying: "#A98FF3",
        psychic: "#F95587",
        bug: "#A6B91A",
        rock: "#B6A136",
        ghost: "#735797",
        dragon: "#6F35FC",
        dark: "#705746",
        steel: "#B7B7CE",
        fairy: "#D685AD"
      };
      
      // Define colors for each stat
      const statColors = {
        hp: "orange",
        attack: "blue",
        defense: "blue",
        "special-attack": "blue",
        "special-defense": "blue",
        speed: "blue"
      };
      
      // Function to get color based on stat name
      function getStatColor(statName) {
        return statColors[statName] || "gray"; // Default to gray if stat not found
      }
      
      // Function to capitalize the first letter of each word
      function capitalize(word) {
        return word.charAt(0).toUpperCase() + word.slice(1);
      }
      
      async function traer() {
        // Reset the arrays before fetching new data
        tipos.value = [];
        estadisticas.value = [];
      
        try {
          let res = await axios.get(`https://pokeapi.co/api/v2/pokemon/${pokemon.value.toLowerCase()}`);
          imgPokemon.value = res.data.sprites.other["official-artwork"].front_default;
          imgPokemon2.value = res.data.sprites.other["showdown"].front_default;
          name.value = res.data.name;
          altura.value = res.data.height;
          peso.value = res.data.weight;
          number.value = res.data.id;
      
          res.data.types.forEach(element => {
            tipos.value.push(element.type.name);
          });
      
          res.data.stats.forEach(element => {
            estadisticas.value.push({
              nombre: element.stat.name,
              cantidad: element.base_stat
            });
          });
      
          // Set the background color based on the first type
          if (tipos.value.length > 0) {
            backgroundColor.value = typeColors[tipos.value[0]] || "#FFFFFF"; // Default to white if type not found
          }
      
        } catch (error) {
          console.error("Error fetching Pokémon data:", error);
        }
      }
      
      // Computed properties to capitalize values
      const capitalizedTipos = computed(() => tipos.value.map(tipo => capitalize(tipo)));
      const capitalizedName = computed(() => capitalize(name.value));
      const capitalizedEstadisticas = computed(() => estadisticas.value.map(stat => ({
        ...stat,
        nombre: capitalize(stat.nombre)
      })));
      
      // Watch for changes in 'tipos' to update the background color
      watch(tipos, (newTipos) => {
        if (newTipos.length > 0) {
          backgroundColor.value = typeColors[newTipos[0]] || "#FFFFFF"; // Default to white if type not found
        }
      });
      </script>
      
      <style>
      #block {
        justify-content: center;
        align-content: center;
      }
      </style>
      



<template>
  <div class="container">
    <header>
      <div class="logo">
        <img id="imageLogo" src="./assets/logo.jpg" alt="">
        <h1>PokeDex</h1>
      </div>
      <div class="head">
        <div class="divSearch">
          <div class="InputContainer">
            <input type="text" v-model="pokemonId" @click="show = false" name="text" class="input" id="input"
              placeholder="Search pokemon">
            <label for="input" @click="listarPokemones()" class="labelforsearch">
              <svg viewBox="0 0 512 512" class="searchIcon">
                <path
                  d="M416 208c0 45.9-14.9 88.3-40 122.7L502.6 457.4c12.5 12.5 12.5 32.8 0 45.3s-32.8 12.5-45.3 0L330.7 376c-34.4 25.2-76.8 40-122.7 40C93.1 416 0 322.9 0 208S93.1 0 208 0S416 93.1 416 208zM208 352a144 144 0 1 0 0-288 144 144 0 1 0 0 288z">
                </path>
              </svg>
            </label>
            <div class="border"></div>
            <button class="micButton"><svg viewBox="0 0 384 512" class="micIcon">
                <path
                  d="M192 0C139 0 96 43 96 96V256c0 53 43 96 96 96s96-43 96-96V96c0-53-43-96-96-96zM64 216c0-13.3-10.7-24-24-24s-24 10.7-24 24v40c0 89.1 66.2 162.7 152 174.4V464H120c-13.3 0-24 10.7-24 24s10.7 24 24 24h72 72c13.3 0 24-10.7 24-24s-10.7-24-24-24H216V430.4c85.8-11.7 152-85.3 152-174.4V216c0-13.3-10.7-24-24-24s-24 10.7-24 24v40c0 70.7-57.3 128-128 128s-128-57.3-128-128V216z">
                </path>
              </svg>
            </button>
          </div>
        </div>
      </div>
    </header>

    <main>
      <div class="pokemonImage">
        <h1 v-show="show" class=" titleImage">{{ name }}</h1>
        <div :style="{ backgroundColor: Elements.length > 0 ? colors(Elements[0].type.name) : '#ffff' }" class="backgroundImage">
          <img id="image" :src="image" alt="" v-show="show">
        </div>

        <div v-show="show === false" class="loader">
          <div class="glitch" data-glitch="Loading...">Loading...</div>
        </div>

        <div id="divWH" v-show="show">
          <p id="weight" v-show="show">Weight: {{ weight }}</p>
          <p id="height" v-show="show">Height: {{ height }}</p>
        </div>
      </div>




      <div :style="{ backgroundImage: `url(${imageBackground})` }" class="pokemonInformation">
          <h1 v-show="show ===false" class="titleLoader">
            Cargando
          </h1>
          <div v-show="show === false" class="loaderr"></div>
        <h3 v-show="show">#{{ id }}</h3>
        <h2 v-show="show">Elements of {{ name }}</h2>
        <p id="Element" v-for="element in Elements" :key="element.type.name"
          :style="{ backgroundColor: colors(element.type.name) }">{{ element.type.name }}</p>
        <h2 v-show="show">Weaknesses of {{ name }}</h2>
        <div class="divWeaknesses">
          <span id="weakness" v-for="(weakness) in Weaknesses" :key="index"
            :style="{ backgroundColor: colors(weakness) }">
            {{ weakness }}
          </span>
        </div>

      </div>

      <div class="pokemonStatistics">
        <p id="Hp" class="statisticsParagraphs">Hp:{{ hp }} / 255</p>
        <div class="HpBar">
          <div id="bar" :style="{ width: hp + 'px' }" ></div>
          </div>
          <p class="statisticsParagraphs">Attack:{{ Attack }} / 255</p>
          <div class="HpBar">
            <div id="bar" :style="{ width: Attack + 'px' }"></div>
          </div>
          <p class="statisticsParagraphs">Defense:{{ Defense }} / 255</p>
          <div class="HpBar">
            <div id="bar" :style="{ width: Defense + 'px' }"></div>
          </div>
          <p class="statisticsParagraphs">Special-Attack:{{ special_Attack }} / 255 </p>
          <div class="HpBar">
            <div id="bar" :style="{ width: special_Attack + 'px' }"></div>
          </div>
          <p class="statisticsParagraphs">Special-Defense:{{ special_Defense }} / 255</p>
          <div class="HpBar">
            <div id="bar" :style="{ width: special_Defense + 'px' }"></div>
          </div>
          <p class="statisticsParagraphs">Speed:{{ speed }} / 255</p>
          <div class="HpBar">
            <div id="bar" :style="{ width: speed + 'px' }"></div>
          </div>
        </div>
    </main>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';

let pokemonId = ref('');
let name = ref('');
let id = ref('');
let image = ref('');
let Elements = ref([]);
let hp = ref('');
let Attack = ref('');
let Defense = ref('');
let special_Attack = ref('');
let special_Defense = ref('');
let speed = ref('');
let show = ref(false);
let height = ref('');
let weight = ref('');
let imageBackground = ref('')
let Weaknesses = ref([])





async function listarPokemones() {
  const pokeapi = `https://pokeapi.co/api/v2/pokemon/${pokemonId.value}`;
  try {
    let { data } = await axios.get(pokeapi);
    name.value = data.forms[0].name;
    image.value = data.sprites.front_default;
    height.value = data.height;
    weight.value = data.weight;
    id.value = data.id;
    Elements.value = data.types;
    hp.value = data.stats[0].base_stat;
    Attack.value = data.stats[1].base_stat;
    Defense.value = data.stats[2].base_stat;
    special_Attack.value = data.stats[3].base_stat;
    special_Defense.value = data.stats[4].base_stat;
    speed.value = data.stats[5].base_stat;
    imageBackground.value = data.sprites.back_default;

    console.log(data);

    const elements = Elements.value.map(element => element.type.name);
    Weaknesses.value = await obtenerDebilidades(elements);
    show.value = true;

  } catch (error) {
    alert('Pokemon not found');
    show.value = false;
  }
}
  
async function obtenerDebilidades(types) {
  try {
    const typeData = await Promise.all(types.map(type => axios.get(`https://pokeapi.co/api/v2/type/${type}`)));

    let debilidadesList = [];
    typeData.forEach(response => {
      debilidadesList.push(...response.data.damage_relations.double_damage_from.map(type => type.name));
    });

    
    const uniqueDebilidades = [...new Set(debilidadesList)];
    return uniqueDebilidades; 
  } catch (e) {
    return [];
  }
}

function colors(type) {
  const typeColors = {
    fire: '#f08030',
    water: '#6890f0',
    grass: '#78c850',
    electric: '#f8d030',
    ice: '#98d8d8',
    fighting: '#c03028',
    poison: '#a040a0',
    ground: '#e0c068',
    flying: '#a890f0',
    psychic: '#f85888',
    bug: '#a8b820',
    rock: '#b8a038',
    ghost: '#705898', 
    dragon: '#7038f8',
    dark: '#705848',
    steel: '#b8b8d0',
    fairy: '#f0b6bc',
    normal: '#a8a878'
  }
  return typeColors[type] || '#bde0fe'
};

</script>

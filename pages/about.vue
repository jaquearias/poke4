
<template>
  <div class="hello">
    <Header/>
    <div class="fondoPrincipalAbout">
      <h1 class="title">Detalles de personaje</h1>
    </div>
    <div>
      <b-table stacked  :fields="columnas" :items="datos" class="custom-table">
        
        <!-- <template #cell(status)="data">
          {{`${data.item.status != "unknown" ? data.item.status : "Unknown"}`}}
        </template>
        <template #cell(species)="data">
          {{`${data.item.species != "unknown" ? data.item.species : "Unknown"}`}}
        </template>
        <template #cell(origin)="data">
          <span v-if="data.item.origin.name === 'unknown'">Unknown</span>
          <span v-else>
            {{`${data.item.origin.type}`}}: {{`${data.item.origin.name}`}} <br> {{`${ (data.item.origin.dimension != 'unknown') ? data.item.origin.dimension : '' }`}}
          </span>
        </template>
        <template #cell(location)="data">
          <span v-if="data.item.location.name === 'unknown'">Unknown</span>
          <span v-else>
            {{`${data.item.location.type}`}}: {{`${data.item.location.name}`}} <br> {{`${ (data.item.location.dimension != 'unknown') ? data.item.location.dimension : '' }`}}
          </span>
        </template>
        <template #cell(episode)="data">
          <ul v-for="(ep, id) in data.item.episode" :key="id">
            <li>
              {{ ep.id }}. {{ ep.name }}
            </li>
          </ul>
        </template> -->
        <template #cell(imagenPersonaje)="data">
          <b-img v-if="data.item.pokemon_v2_pokemonsprites_aggregate.nodes[0].sprites!=null" :src="`${data.item.pokemon_v2_pokemonsprites_aggregate.nodes[0].sprites}`" />
          <b-img v-else :src="'https://media.istockphoto.com/id/1055079680/vector/black-linear-photo-camera-like-no-image-available.jpg?s=612x612&w=0&k=20&c=P1DebpeMIAtXj_ZbVsKVvg-duuL0v9DlrOZUvPG6UJk='"/>
        </template>
        <template #cell(generation)="data">
          {{ `${data.item.pokemon_v2_pokemonspecy.pokemon_v2_generation.name}` }} 
        </template>
        <template #cell(color)="data">
          {{ `${data.item.pokemon_v2_pokemonspecy.pokemon_v2_pokemoncolor.name}` }} 
        </template>
        <template #cell(height)="data">
          {{ `${data.item.height/10}` }} m
        </template>
        <template #cell(weight)="data">
          {{ `${data.item.weight/10}` }} kg
        </template>
        <template #cell(pokemon_v2_pokemontypes_aggregate)="data">
          <ul class="tiposPokemon" v-for="(type, id) in data.item.pokemon_v2_pokemontypes_aggregate.nodes" :key="id">
            <li > {{type.pokemon_v2_type.name}} </li>
          </ul>
        </template>
        <template #cell(pokemon_v2_pokemonabilities)="data">
          <ul class="tiposPokemon" v-for="(hab, id) in data.item.pokemon_v2_pokemonabilities" :key="id">
            <li > {{hab.pokemon_v2_ability.name}} </li>
          </ul>
        </template>
        <template #cell(hp)="data">
          <div class="centrarBarra">
            {{ `${data.item.pokemon_v2_pokemonstats[0].base_stat}` }}
            <b-form-input id="range_hp" type="range" :value=data.item.pokemon_v2_pokemonstats[0].base_stat></b-form-input>
          </div>
        </template>
        <template #cell(attack)="data">
          <div class="centrarBarra">
            {{ `${data.item.pokemon_v2_pokemonstats[1].base_stat}` }}
            <b-form-input id="range_hp" type="range" :value=data.item.pokemon_v2_pokemonstats[1].base_stat></b-form-input>
          </div>
        </template>
        <template #cell(defense)="data">
          <div class="centrarBarra">
            {{ `${data.item.pokemon_v2_pokemonstats[2].base_stat}` }}
            <b-form-input id="range_hp" type="range" :value=data.item.pokemon_v2_pokemonstats[2].base_stat></b-form-input>
          </div>
        </template>
        <template #cell(special-attack)="data">
          <div class="centrarBarra">
            {{ `${data.item.pokemon_v2_pokemonstats[3].base_stat}` }}
            <b-form-input id="range_hp" type="range" :value=data.item.pokemon_v2_pokemonstats[3].base_stat></b-form-input>
          </div>
        </template>
        <template #cell(special-defense)="data">
          <div class="centrarBarra">
            {{ `${data.item.pokemon_v2_pokemonstats[4].base_stat}` }}
            <b-form-input id="range_hp" type="range" :value=data.item.pokemon_v2_pokemonstats[4].base_stat></b-form-input>
          </div>
        </template>
        <template #cell(speed)="data">
          <div class="centrarBarra">
            {{ `${data.item.pokemon_v2_pokemonstats[5].base_stat}` }}
            <b-form-input id="range_hp" type="range" :value=data.item.pokemon_v2_pokemonstats[5].base_stat></b-form-input>
          </div>
        </template>
        <template #cell(evolution)="data">
          <ul class="pokemonEvolucion" v-for="(type, id) in data.item.pokemon_v2_pokemonspecy.pokemon_v2_evolutionchain.pokemon_v2_pokemonspecies" :key="id">
            <li @click="send(type.id)"> 
              {{type.name}} 
              <b-img :src="`${type.pokemon_v2_pokemons[0].pokemon_v2_pokemonsprites_aggregate.nodes[0].sprites}`" />
            </li>
          </ul>
        </template>
      </b-table>
        <div class="contieneBoton">
            <BButton size="lg" class="botonRosa2" @click="regresar()" >Characters List</BButton>
        </div>
    </div>
  </div>
</template>

<script>
import Header from '~/components/Header.vue'

export default {
    components: {
      Header
    },
    data() {
      return {
        id: 1,
        datos: null,
        pokemonId: null,
        columnas: [
          {
            label: "Pokemon",
            key: "id"
          },
          {
            label: "Nombre",
            key: "name"
          },
          {
            label: "Imagen",
            key: "imagenPersonaje"
          },
          {
            label: "Generación",
            key: "generation"
          },
          {
            label: "Color",
            key: "color"
          },
          {
            label: "Altura",
            key: "height"
          },
          {
            label: "Peso",
            key: "weight"
          },
          {
            label: "Tipo",
            key: "pokemon_v2_pokemontypes_aggregate"
          },
          {
            label: "Habilidades",
            key: "pokemon_v2_pokemonabilities"
          },
          {
            label: "Estadísticas"
          },
          {
            label: "HP",
            key: "hp"
          },
          {
            label: "Attack",
            key: "attack"
          },
          {
            label: "Defense",
            key: "defense"
          },
          {
            label: "Special Attack",
            key: "special-attack"
          },
          {
            label: "Special Defense",
            key: "special-defense"
          },
          {
            label: "Speed",
            key: "speed"
          },
          {
            label: "Evoluciones",
            key: "evolution"
          }
        ]
      };
    },
    head() {
      return {
        title: 'Detalles'
      }
    },
    mounted() {
      //se activará después de que se encuentre montado
      // console.log("El id es: "+ this.$route.params.id);
      // console.log("La pagina del personaje es: "+ this.$route.params.page);
      // console.log("La cadena de filtros ess: "+this.$route.params.filtros);

      this.pokemonId = this.$route.query.id;

      this.getInfo();
    },
    methods: {
      async getInfo() {
        var queryVar1 = `
            query Pokemon_v2_pokemon {
                pokemon_v2_pokemon(where: { id: { _eq: ${this.pokemonId} } }) {
                    height
                    id
                    name
                    weight
                    pokemon_v2_pokemonabilities {
                        pokemon_v2_ability {
                            name
                        }
                    }
                    pokemon_v2_pokemonsprites_aggregate {
                        nodes {
                            sprites(path: "other.official-artwork.front_default")
                        }
                    }
                    pokemon_v2_pokemonstats {
                        base_stat
                        pokemon_v2_stat {
                            name
                        }
                    }
                    pokemon_v2_pokemontypes_aggregate {
                        nodes {
                            pokemon_v2_type {
                                name
                            }
                        }
                    }
                }
            }
          `;
        var queryVar2 = `
            query Pokemon_v2_pokemon {
              pokemon_v2_pokemon(where: { id: { _eq: ${this.pokemonId} } }) {
                height
                id
                name
                weight
                pokemon_v2_pokemontypes_aggregate {
                  nodes {
                    pokemon_v2_type {
                      name
                    }
                  }
                }
                pokemon_v2_pokemonstats {
                  base_stat
                  pokemon_v2_stat {
                    name
                  }
                }
                pokemon_v2_pokemonabilities {
                  pokemon_v2_ability {
                    name
                  }
                }
                pokemon_v2_pokemonsprites_aggregate {
                  nodes {
                    sprites(path: "other.official-artwork.front_default")
                  }
                }
                pokemon_v2_pokemonspecy {
                  pokemon_v2_evolutionchain {
                    pokemon_v2_pokemonspecies {
                      name
                      id
                    }
                  }
                  pokemon_v2_pokemoncolor {
                    name
                    id
                  }
                  generation_id
                  pokemon_v2_generation {
                    name
                    id
                  }
                }
              }
              pokemon_v2_pokemon_aggregate {
                aggregate {
                  count
                }
              }
            }
          `;
        var queryVar = `
          query Pokemon_v2_pokemon {
            pokemon_v2_pokemon(where: {id: {_eq: ${this.pokemonId} }}) {
              height
              id
              name
              weight
              pokemon_v2_pokemontypes_aggregate {
                nodes {
                  pokemon_v2_type {
                    name
                  }
                }
              }
              pokemon_v2_pokemonstats {
                base_stat
                pokemon_v2_stat {
                  name
                }
              }
              pokemon_v2_pokemonabilities {
                pokemon_v2_ability {
                  name
                }
              }
              pokemon_v2_pokemonsprites_aggregate {
                nodes {
                  sprites(path: "other.official-artwork.front_default")
                }
              }
              pokemon_v2_pokemonspecy {
                pokemon_v2_evolutionchain {
                  pokemon_v2_pokemonspecies {
                    name
                    id
                    pokemon_v2_pokemons {
                      pokemon_v2_pokemonsprites_aggregate {
                        nodes {
                          sprites(path: "other.official-artwork.front_default")
                        }
                      }
                    }
                  }
                }
                pokemon_v2_pokemoncolor {
                  name
                  id
                }
                generation_id
                pokemon_v2_generation {
                  name
                  id
                }
              }
            }
            pokemon_v2_pokemon_aggregate {
              aggregate {
                count
              }
            }
          }
        `;
        // console.log(queryVar);
        const response = await this.$axios.$post('https://beta.pokeapi.co/graphql/v1beta', { query: queryVar });
        console.log(response.data.pokemon_v2_pokemon);
        this.datos = response.data.pokemon_v2_pokemon;
        // console.log(response.data.characters.info.count);
      },
      regresar() {
        this.$router.push({ name: 'index'});
		  },
      send(idValue) {
        console.log("Se quiere ir: "+idValue)
        this.$router.push({ path: 'about', query: { id: idValue } });
        this.pokemonId = this.$route.query.id;
        this.getInfo();
      }
    }
}
</script>

<style>

li {
  display: inline-block;
  margin: 0 10px;
}

.hello img {
  width: 400px; 
  height: auto; 
  border-radius: 10px;
}


.title {
  /* background-color: #FA8072; */
  /* margin-bottom: 10px; */
  color: white;
  padding-top: 20px;
}

.b-table { 
  text-align: center; 
  vertical-align: middle; 
}

.titulo {
  color: rgb(251, 149, 166);
  font-weight: bold;
  padding: 10px;
  margin: 20px;
  background: rgb(250, 250, 250);
}

.botonRosa2 {
  background : #FA8072 !important;
  border: solid rgb(255, 255, 255) !important;
}

.botonRosa2:hover {
  background : #f15c4c !important;
  border: solid 1px #fdb4bf !important;
}

.contieneBoton {
  display: flex;
  justify-content: center; 
  margin-bottom: 50px;
}

.fondoPrincipalAbout {
  background-color: #FA8072;
  margin-bottom: 15px;
  margin-top: 10px;
  text-align: center;
  padding-bottom: 20px;
}

.centrarBarra {
  display: flex;
  justify-content: center;
  font-weight: bold;
  font-size: 18px;
  margin: 10px;
}

.centrarBarra input {
  margin-left: 20px;
  margin-right: 20px;
  width: 60%;
}

.pokemonEvolucion li {
  background-color: #98FB98;
	border-radius: 200px; 
  width: 40%;
  font-weight: bold;
  border: solid 6px #73e773;
}

.pokemonEvolucion li:hover {
  background-color: #73e773;
  cursor: pointer;
  border: solid 6px #98FB98;
}

.pokemonEvolucion img{
  width: 200px;
  height: auto;
  margin-left: 50px;
}

</style>

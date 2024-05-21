
<template>
	<div>
		<div class="rowsCont">
			<label for="sortSelect">Ordenar por:      </label>
      <b-form-select id="sortSelect" v-model="sort" :options="optionsSort" ></b-form-select>
			<label for="rows-input">Número de registros:      </label>
			<b-form-input id="rows-input" v-model="perPage" type="number" value="20" min="1" max="30"></b-form-input>
		</div>
		<b-table id="listaPokemon" striped hover bordered light :fields="columnas" :items="copyDatos" :current-page="currentPage" @input="emiteNewCount" :per-page="perPage" :filter="searchString" @filtered="onFiltered" ref="table" label-sort-asc="" label-sort-desc="" label-sort-clear="">
			<template #cell(imagenPersonaje)="data">
				<b-img v-if="data.item.pokemon_v2_pokemonsprites_aggregate.nodes[0].sprites!=null" :src="`${data.item.pokemon_v2_pokemonsprites_aggregate.nodes[0].sprites}`" />
				<b-img v-else :src="'https://media.istockphoto.com/id/1055079680/vector/black-linear-photo-camera-like-no-image-available.jpg?s=612x612&w=0&k=20&c=P1DebpeMIAtXj_ZbVsKVvg-duuL0v9DlrOZUvPG6UJk='"/>
			</template>
			<template #cell(generacionP)="data">
				{{ `${data.item.pokemon_v2_pokemonspecy.pokemon_v2_generation.name}` }}
			</template>
			<template #cell(colorP)="data">
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
					<li > {{ type.pokemon_v2_type.name }} </li>
				</ul>
			</template>
			<template #cell(boton)="data">
				<b-button class="botonRosa" @click="send(data.item.id)">Info</b-button>
			</template>
		</b-table>
		<div>
			<b-pagination v-model="currentPage" :current-page="currentPage" pills size="lg" align="center" :total-rows="rows" :per-page="perPage" aria-controls="listaPokemon"></b-pagination>
		</div>
	</div>
</template>

<script>
export default {
	name: 'ListaPersonajes',
	props: {
		filterArr: [],
		searchString: String
	},
	data() {
		return {
			datos: [],
			copyDatos: [],
			rows: 20,
      currentPage: 1,
			perPage: 20,
      query: null,
      sort: 3,
			count: null,
			copyFiltro: " ",
			columnas: [
        {
          label: "ID",
          key: "id",
		  sortable: true
        },
        {
          label: "Pokemon",
          key: "imagenPersonaje"
        },
        {
          label: "Nombre",
          key: "name",
		  sortable: true
        },
        {
          label: "Generación",
          key: "generacionP"
        },
        {
          label: "Color",
          key: "colorP"
        },
        {
          label: "Altura",
          key: "height",
		  sortable: true
        },
        {
          label: "Peso",
          key: "weight",
		  sortable: true
        },
        {
          label: "Tipo",
          key: "pokemon_v2_pokemontypes_aggregate"
        },
        {
          label: "See more",
          key: "boton"
        },
      ],
      optionsSort: [
        { value: 1, text: 'A - Z' },
        { value: 2, text: 'Z - A' },
        { value: 3, text: 'ID ascendente' },
        { value: 4, text: 'ID descendente' }
      ]
		}
	},
	mounted() {
		this.checkPage();
		this.getData();
		localStorage.setItem('sortActive', this.sort);
		console.log("SortActive: "+localStorage.getItem('sortActive'));
	},
	computed: {
		getTableData(){
			console.log("EJECUTANDO COMPUTED: "+this.currentPage);
			this.scrollToTop();
      // localStorage.setItem('currentPage', this.currentPage);
      // console.log("La localStorage es en COMPUTED: "+localStorage.getItem('currentPage'));
			// console.log("los filtros actuales son: "+this.filterArr);
			return () => this.getData();
		},
		actualiza(){
			// console.log("EJECUTANDO COMPUTED2: "+this.copyFilter);
			this.scrollToTop();
			return () => this.getTableData();
		}
	},
	methods: {
		async getData() {
			// console.log(queryVar);
			if(this.query == null){
				this.ordenaDatos();
			}
			const response = await this.$axios.$post('https://beta.pokeapi.co/graphql/v1beta', { query: this.query });
			// console.log(response);
			this.datos = response.data.pokemon_v2_pokemon;
      // console.log(response.data.pokemon_v2_pokemon);
			this.rows = this.datos.length;
			return response.data.pokemon_v2_pokemon
		},
		ordenaDatos() {
			var sortString;
      console.log("El valor de sort es: "+this.sort);
      switch(this.sort){
        case 1: 
          sortString = '{name: asc}';
          break;
        case 2: 
          sortString = '{name: desc}';
          break;
        case 3: 
          sortString = '{id: asc}';
          break;
        case 4: 
          sortString = '{id: desc}';
          break;
        default:
          sortString = '{name: asc}';
          console.log("Switch no funciona");
      }
      this.query = `
        query Pokemon_v2_pokemon {
          pokemon_v2_pokemon(order_by: ${sortString}) {
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
            pokemon_v2_pokemonsprites_aggregate {
              nodes {
                sprites(path: "other.official-artwork.front_default")
              }
            }
            pokemon_v2_pokemonspecy {
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
      // console.log("QUERY: "+this.query);
		},
		// actualizaSort() {
    //   localStorage.setItem('sortActive', this.sort);
		// },
		emiteNewCount() {
			this.$emit('cambia-count', this.rows);
			// console.log("El valor emitido: "+this.rows);
		},
		scrollToTop() {
      // Hacer scroll suave al principio de la página
      if (process.client) {
        window.scrollTo({
          top: 0,
          behavior: 'smooth'
        });
      }
		},
		send(idValue) {
			this.$router.push({ path: 'about', query: { id: idValue } });
		},
		checkPage(){
      var pageLS = localStorage.getItem('currentPage');
			if(pageLS === null){
				pageLS = 1;
			}
      this.currentPage = pageLS;

			var sortLS = localStorage.getItem('sortActive');
			if(sortLS === null){
				sortLS = 1;
			}
      this.sort = sortLS;

      
      var perPageLS = localStorage.getItem('perPage');
			if(perPageLS === null){
				perPageLS = 20;
			}
      this.perPage = perPageLS;
      

      // console.log("La página en check: "+pageLS);

			if(this.$route.params.filtros){
				
				this.copyFiltro = this.$route.params.filtros;
			}
		},
		onFiltered(filteredItems) {
			// Trigger pagination to update the number of buttons/pages due to filtering
			this.rows = filteredItems.length
			this.currentPage = 1
			this.emiteNewCount();
		},
		actualizaTabla(){
			var auxArr = [];
			var arregloFiltrado = [];
      // console.log(this.filterArr);
			if(this.filterArr[0].length!=0){ //Altura
				this.filterArr[0].forEach(element => {
					this.copyDatos = this.datos;
					switch(element){
						case 1: // Menos de 1 metro
							auxArr = this.copyDatos.filter(function(row) { 
								return row.height < 10
							});
							break;
						case 2: // Entre 1 y 2 metros
							auxArr = this.copyDatos.filter(function(row) { 
								return row.height >= 10 && row.height < 20
							});
							break;
						case 3: // Entre 2 y 5 metros
							auxArr = this.copyDatos.filter(function(row) { 
								return row.height >= 20 && row.height < 50
							});
							break;
						case 4: // Más de 5 metros
							auxArr = this.copyDatos.filter(function(row) { 
								return row.height >= 50
							});
							break;
						default:
							console.log("No sirve case de altura filtros");
					}
          console.log("auxArr");
          console.log(auxArr);
					arregloFiltrado = arregloFiltrado.concat(auxArr);
          console.log("arregloFiltrado: ");
          console.log(arregloFiltrado);
					this.copyDatos = arregloFiltrado;
          console.log("copyDatos");
          console.log(this.copyDatos);
			    this.$refs.table.refresh();
					this.rows = this.copyDatos.length;
          console.log("Rows: "+this.rows)
          this.$root.$emit('bv::refresh::table', 'listaPokemon')
				});
			}
			if(this.filterArr[1].length!=0){ //Peso
				this.filterArr[1].forEach(element => {
					this.copyDatos = this.datos;
					switch(element){
						case 1: // Menos de 10 kg
							auxArr = this.copyDatos.filter(function(row) { 
								return row.weight < 100
							});
							break;
						case 2: // Entre 10 y 100 kg
							auxArr = this.copyDatos.filter(function(row) { 
								return row.weight >= 100 && row.weight < 1000
							});
							break;
						case 3: // Entre 100 y 300 kg
							auxArr = this.copyDatos.filter(function(row) { 
								return row.weight >= 1000 && row.weight < 3000
							});
							break;
						case 4: // Más de 300 kg
							auxArr = this.copyDatos.filter(function(row) { 
								return row.weight >= 3000
							});
							break;
						default:
							console.log("No sirve case de altura filtros");
					}
					arregloFiltrado = arregloFiltrado.concat(auxArr);
					this.copyDatos = arregloFiltrado;
					this.rows = this.copyDatos.length;
				});
			}
			if(this.filterArr[2].length!=0){ //Generacion
				this.filterArr[2].forEach(element => {
					this.copyDatos = this.datos;
					
					auxArr = this.copyDatos.filter(fila => this.filterArr[2].some(gen => gen === fila.pokemon_v2_pokemonspecy.pokemon_v2_generation.name));

					arregloFiltrado = arregloFiltrado.concat(auxArr);
					this.copyDatos = arregloFiltrado;
					this.rows = this.copyDatos.length;
				});
			}
			if(this.filterArr[3].length!=0){ //Tipos
				this.filterArr[3].forEach(element => {
					this.copyDatos = this.datos;
					// console.log(this.copyDatos);
					auxArr = this.copyDatos.filter(fila => this.filterArr[3].some(gen => gen === fila.pokemon_v2_pokemontypes_aggregate.nodes[0].pokemon_v2_type.name));

					arregloFiltrado = arregloFiltrado.concat(auxArr);
					this.copyDatos = arregloFiltrado;
					this.rows = this.copyDatos.length;
				});
			}
			else {
				this.copyDatos = this.datos;
			}
			// this.copyDatos.filter(row => row.height < 100);
			// console.log(auxArr);
			this.rows = this.copyDatos.length;
			this.$refs.table.refresh();
		}
	},
	watch: {
		filterArr: function() {
			console.log("CAMBIA FILTER STRING: "+ this.filterArr);
			// this.copyFiltro = this.filterArr;
			// this.currentPage = 0;
			// this.currentPage = 1;
			// this.copyFilter = this.filterArr;
			// console.log("COPY STRING: "+ this.copyFilter);
			// this.getTableData();
			console.log("WATCH filterArr");
			this.actualizaTabla();
		}
    ,
    currentPage: function() {
      console.log("La currP: "+this.currentPage);
      localStorage.setItem('currentPage', this.currentPage);
      console.log("La localStorage nueva: "+localStorage.getItem('currentPage'));
    },
    perPage: function(){
      localStorage.setItem('perPage', this.perPage);
      console.log("La PP localStorage nueva: "+localStorage.getItem('perPage'));
    },
    sort: function () {
      this.ordenaDatos();
      this.getTableData();
			localStorage.setItem('sortActive', this.sort);
    },
		datos: function() {
			this.copyDatos = this.datos;
		}
  }
		// ,
		// searchString: function() {
		// 	console.log("CAMBIA SEARCH STRING: "+ this.searchString);
		// 	// this.currentPage = 0;
		// 	// this.currentPage = 1;
		// 	// this.copyFilter = this.filterArr;
		// 	// console.log("COPY STRING: "+ this.copyFilter);
		// 	// this.getTableData();

		// 	// Código para buscar con searchString
		// }
}
</script>


<style>
img {
  width: 120px; 
  height: auto; 
}

ul {
  list-style-type: none;
	display: flex;
	justify-content: center;
	vertical-align: middle;
	padding: 0%;
}

.tiposPokemon li {
	background-color: #fdb4bf7c;
	padding: 7px;
	padding-bottom: 10px;
	align-content: center;
	justify-content: center;
	vertical-align: middle;
	width: 70%;
	border-radius: 15px;
}

.b-table tr {
  text-align: center; /* Centrado horizontal */
  vertical-align: middle; /* Centrado vertical */
}

.titulo {
  color: rgb(251, 149, 166);
  font-weight: bold;
  padding: 10px;
  margin: 20px;
  background: rgb(250, 250, 250);
}

.count {
  background-color: rgb(224, 224, 224);
  padding: 10px;
  margin: 20px;
  font-weight: bold;
}

.botonRosa {
  background : #FA8072 !important;
  border: solid 1px #fdb4bf !important;
  height: 40px;
}

.botonRosa:hover {
  background : #FA8072 !important;
  border: solid 1px #fdb4bf !important;
}

.b-pagination button { /* estilos para todos los botones */
  background-color: #f8f9fa; /* Color de fondo de los botones */
  border-color: #fdb4bf; /* Color del borde del botón activo */
  color: #e57d90; /* Color del texto de los botones */
}

.b-pagination button:hover {
  color: #ffffff; 
  border-color: #fdb4bf; 
  background-color: #fdb4bf; /* Cambiar color de fondo en hover */
  font-weight: bold;
}

.page-item.active .page-link { /* el boton de la página actual */
    color: #fff;
    background-color: #FA8072;
    border-color: #e57d90;
    font-weight: bold;
}

.page-link.active, .active > .page-link {
    border-color: #ed3591;
    background-color: #ed3591; /* Color de fondo del botón activo */
    color: white;
}

.rowsCont {
	background-color: #87CEFA;
	padding: 15px;
	display: flex;
  justify-content: right; 
	align-items: center;
}

.rowsCont input {
	width: 150px;
	vertical-align: middle;
	margin-left: 20px;
}

.rowsCont select {
	width: 150px;
  height: 38px;
	vertical-align: middle;
	margin-left: 20px;
  margin-right: 20px;
  border-radius: 8px;
  border: none;
  padding: 7px;
  font-size: 15px;
}

.rowsCont label {
	vertical-align: middle;
	color: whitesmoke;
	font-weight: bold;
	font-size: 20px;
}

</style>

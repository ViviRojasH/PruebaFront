<template>
<div>
    <h1>Rick and Morty</h1>
    <br>
    <b-container class="bv-example-row container">
        <b-row class="row">
            <template v-for="(person, index) in personajes">
                <b-col cols="3" :key="index" deck class="col">
                    <b-card :img-src="person.image" :title="person.name">
                        <b-button href="#" variant="primary" v-b-modal.infoPersonaje v-on:click="verInfo(person)">Más información</b-button>
                    </b-card>
                    <br>
                </b-col>
            </template>
        </b-row>

        <nav aria-label="Page navigation example">
            <ul class="pagination">
                <li class="page-item"><a class="page-link" @click="paginacion(numPage - 1)">Anterior</a></li>
                <li class="page-item"><a class="page-link">{{numPage}}</a></li>
                <li class="page-item"><a class="page-link" @click="paginacion(numPage + 1)">Siguiente</a></li>
            </ul>
        </nav>
    </b-container>
    <b-modal ok-only id="infoPersonaje" size="lg" scrollable=true>
        <h1>{{dataPersonaje.nombre}}</h1>
        <b-card v-b-scrollspy :img-src="dataPersonaje.imagen">
            <b-card-text>
                <strong>Estado:</strong> {{dataPersonaje.estado}}<br>
                <strong>Especie:</strong> {{dataPersonaje.especie}}<br>
                <strong>Genero:</strong> {{dataPersonaje.genero}}
                <h2>Capitulos:</h2>
                <b-card-body>
                    <template v-for="(capitulo, index) in dataCapitulos">
                        <b-list-group :key="index">
                            <b-list-group-item href="#" class="flex-column align-items-start">
                                <div class="d-flex w-100 justify-content-between">
                                    <h5 class="mb-1">{{capitulo.name}}</h5>
                                    <small>Fecha de publicación: {{capitulo.air_date}}</small>
                                </div>
                                <p class="mb-1">
                                    <strong>Episodio</strong> {{capitulo.episode}}
                                </p>
                            </b-list-group-item>
                        </b-list-group>
                    </template>
                </b-card-body>
            </b-card-text>
        </b-card>
    </b-modal>
</div>
</template>

<script>
export default {

    data() {
        return {
            personajes: [],
            numPage: 1,
            ulrNext: "",
            urlPrevius: "",
            dataPersonaje: {},
            dataCapitulos: {}
        }
    },
    methods: {
        //obtener lista de personajes
        traerPersonajes() {
            const parametroNumPag = {
                numPage: this.numPage
            }
            fetch('https://rickandmortyapi.com/api/character/?page=1')
                .then(response => response.json())
                .then(res => {
                    console.log(res)
                    this.personajes = res.results
                    this.ulrNext = res.info.next
                    this.urlPrevius = res.info.prev
                });
        },
        //obtener info del personaje individual
        verInfo(personaje) {
            fetch('https://rickandmortyapi.com/api/character/' + personaje.id)
                .then(response => response.json())
                .then(res => {
                    debugger
                    console.log(res)
                    this.dataPersonaje = {
                        nombre: res.name,
                        imagen: res.image,
                        estado: res.status,
                        especie: res.species,
                        genero: res.gender,
                        capitulos: res.episode
                    }
                    //recorrer capitulos recibidos y obtener ids
                    var episodio = "";
                    this.dataPersonaje.capitulos.forEach(element => {
                        episodio += element.replace("https://rickandmortyapi.com/api/episode/", "") + ","
                    });
                    episodio = episodio.substring(0, episodio.length - 1)
                    //ejecutar funcion enviando ids de episodios
                    this.ConsultarCaptitulos(episodio)
                });

        },
        linkGen(pageNum) {

        },
        paginacion(numPage) { //recibe número de pagina actual

            if (this.numPage <= 0 || numPage > this.numPage) { //valida si esta en el rango y carga siguiente pagina
                fetch(this.ulrNext)
                    .then(response => response.json())
                    .then(res => {
                        this.personajes = res.results
                        this.ulrNext = res.info.next
                        this.urlPrevius = res.info.prev
                        this.numPage = numPage
                    });
            } else if (numPage < this.numPage && numPage != 0) { //valida si esta en el rango y carga pagina anterior

                fetch(this.urlPrevius)
                    .then(response => response.json())
                    .then(res => {

                        this.personajes = res.results
                        this.ulrNext = res.info.next
                        this.urlPrevius = res.info.prev
                        this.numPage = numPage
                    });
            }

        },
        ConsultarCaptitulos(caps) { //recibe string con ids de los capitulos y trae los capitulos correspondientes al personaje
            fetch("https://rickandmortyapi.com/api/episode/" + caps)
                .then(response => response.json())
                .then(res => {
                    this.dataCapitulos = res
                    console.log(this.dataCapitulos)
                });
        },

    },
    mounted: function () { //ejecuta el metodo cuando se termina de montar la pagina
        this.traerPersonajes(2)
    }

}
</script>

<style>

</style>

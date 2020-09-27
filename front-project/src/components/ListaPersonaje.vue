<template>
<div>
    <h1>Personajes Component</h1>
    <br>
    <b-container class="bv-example-row">
        <b-row>

            <template v-for="(person, index) in personajes">
                <b-col cols="3" :key="index" deck>
                    <b-card :img-src="person.image" :title="person.name">
                        <b-button href="#" variant="primary" v-b-modal.infoPersonaje v-on:click="verInfo(person)">Biografia</b-button>
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
    <b-modal ok-only id="infoPersonaje" title="Infomacón Personaje">
        <h1>{{dataPersonaje.nombre}}</h1>
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
            dataPersonaje: {}
        }
    },

    methods: {

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
        verInfo(personaje) {
            //alert(personaje.name + personaje.status + personaje.species);
            fetch('https://rickandmortyapi.com/api/character/' + personaje.id)
                .then(response => response.json())
                .then(res => {
                    console.log(res)
                    this.dataPersonaje = {
                        nombre: res.name
                    }
                });
        },
        linkGen(pageNum) {
            console.log(pageNum)
        },
        paginacion(numPage) {
            debugger
            if (this.numPage <= 0 || numPage > this.numPage) {

                fetch(this.ulrNext)
                    .then(response => response.json())
                    .then(res => {
                        debugger
                        console.log(res)
                        this.personajes = res.results
                        this.ulrNext = res.info.next
                        this.urlPrevius = res.info.prev
                        this.numPage = numPage
                    });
            } else if (numPage < this.numPage && numPage != 0) {

                fetch(this.urlPrevius)
                    .then(response => response.json())
                    .then(res => {
                        console.log(res)
                        this.personajes = res.results
                        this.ulrNext = res.info.next
                        this.urlPrevius = res.info.prev
                        this.numPage = numPage
                    });
            }

        },

    },
    mounted: function () {
        this.traerPersonajes(2)
    }

}
</script>

<style>

</style>

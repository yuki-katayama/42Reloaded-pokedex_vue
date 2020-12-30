<template>
<div>
    <div v-if="errored">{{Errmassage}}</div>

    <div v-else>
        <div v-if="loading">Loading...</div>
        <div v-else v-for="pokedex in pokedexList" :key="pokedex.id">
            <p>{{pokedex.id}}</p>
            <p>{{pokedex.name}}</p>
            <img :src="pokedex.image">
        </div>
    </div>
</div>
</template>

<script>
import axios from 'axios'
export default {
    data: function () {
        return {
            url: [],
            pokedexList: {},
            Errmassage: '',
            loading: true,
            errored: false,
        };
    },
    methods: {},
    computed: {},
    mounted: async function () {
        let pokedexAPI;
        await axios.get('https://pokeapi.co/api/v2/pokemon')
            .then(response => pokedexAPI = response.data.results)
            .catch(error => {
                this.Errmassage = error
                this.errored = true
            })
        if (this.errored) {
            return
        }
        for (let i = 0; i < pokedexAPI.length; i++) {
            this.url.push(pokedexAPI[i].url)
        }
        for (let i = 0; i < this.url.length; i++) {
            await axios.get(this.url[i])
                .then(response => pokedexAPI = response.data)
                .catch(error => {
                    this.Errmassage = error
                    this.errored = true
                })
            if (this.errored) {
                return
            }
            this.pokedexList[pokedexAPI.name] = {}
            this.pokedexList[pokedexAPI.name]["name"] = pokedexAPI.name
            this.pokedexList[pokedexAPI.name]["id"] = pokedexAPI.id
            this.pokedexList[pokedexAPI.name]["image"] = pokedexAPI.sprites.front_shiny
        }
        console.log(this.pokedexList)
        this.loading = false
        return;
    }
};
</script>

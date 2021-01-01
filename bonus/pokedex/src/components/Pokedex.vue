<template>
<div>
    <div v-if="errored">{{Errmassage}}</div>
    <div v-else>
        <div v-if="loading">Loading...</div>
        <div v-else v-for="pokedex in pokedexList" :key="pokedex.id">
        <!-- <router-link to="/pokemon/:id">{{pokedex.name}}</router-link> -->
		<router-link :to="{name: 'pokemon', params: {id: pokedex.id, object: pokedex}}">{{pokedex.name}}</router-link>
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
            pokedexList: {},
            Errmassage: '',
            loading: true,
            errored: false,
            url: []
        };
    },
    methods: {},
    computed: {},
    mounted: async function () {
        let pokedexAPI;
        for (let i = 1; i < 450; i++) {
            this.url.push("https://pokeapi.co/api/v2/pokemon/" + i);
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
            this.pokedexList[pokedexAPI.name]["image"] = pokedexAPI.sprites.front_default
        }
        console.log(this.pokedexList)
        this.loading = false
        return;
    }
};
</script>

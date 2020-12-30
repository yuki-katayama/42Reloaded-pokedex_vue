<template>
<div>
    <div v-if="errored">{{Errmassage}}</div>
    <div v-else>
        <div v-if="loading">Loading...</div>
        <div v-else v-for="pokedex in pokedexList" :key="pokedex.id">
        <!-- <router-link to="/pokemon/:id">{{pokedex.name}}</router-link> -->
		<router-link :to="{name: 'pokemon', params: {id: pokedex.id, object: pokedex}}">{{pokedex.name}}</router-link>
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
			this.pokedexList[pokedexAPI[i].name] = {}
			this.pokedexList[pokedexAPI[i].name]["id"] = i + 1
			this.pokedexList[pokedexAPI[i].name]["name"] = pokedexAPI[i].name
			this.pokedexList[pokedexAPI[i].name]["url"] = pokedexAPI[i].url
		}
		console.log(this.pokedexList)
        this.loading = false
        return;
    }
};
</script>

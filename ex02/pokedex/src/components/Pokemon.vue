<template>
<div>
    <router-link :to="{name: 'pokedex'}">ポケモン選択に戻る</router-link>
    <div v-if="errored">{{Errmassage}}</div>

    <div v-else>
        <div v-if="loading">Loading...</div>
        <div v-else>
            <div class="id">
                <h2>ポケモンID</h2>
                <p>{{detail.id}}</p>
            </div>
            <div class="name">
                <h2>名前 </h2>
                <p>{{detail.name}}</p>
            </div>
            <div class="abilities">
                <h2>技</h2>
                <div v-for="(abilities, index) in detail.abilities" :key="abilities.id">
                    <p>{{index + 1}} : {{abilities.ability.name}}</p>
                </div>
            </div>
            <div class="picture">
                <h2>画像</h2> <img :src="detail.image">
            </div>
            <div class="type">
                <h2>タイプ: </h2>
                <div v-for="(type, index) in detail.types" :key="type.id">
                    <p>{{index + 1}} : {{type.type.name}}</p>
                </div>
            </div>
            <div class="stats">
                <h2>ステータス</h2>
                <div v-for="(stats) in detail.stats" :key="stats.id">
                    <p>{{stats.stat.name}} : {{stats.base_stat}}</p>
                </div>
            </div>
        </div>
    </div>
</div>
</template>

<script>
import axios from 'axios'
export default {
    props: {
        pokemonData: {
            type: Object,
            //   require: true
        }
    },
    data: function () {
        return {
            detail: {},
            Errmassage: '',
            loading: true,
            errored: false,
        };
    },
    methods: {},
    computed: {},
    mounted: async function () {
        let pokedexAPI;
        await axios.get('https://pokeapi.co/api/v2/pokemon/' + this.pokemonData.id)
            .then(response => pokedexAPI = response.data)
            .catch(error => {
                this.Errmassage = error
                this.errored = true
            })
        if (this.errored) {
            return
        }
        console.log(pokedexAPI)
        for (let i = 0; i < pokedexAPI.length; i++) {
            this.url.push(pokedexAPI[i].url)
        }
        this.detail["id"] = pokedexAPI.id;
        this.detail["name"] = pokedexAPI.name;
        this.detail["image"] = pokedexAPI.sprites.front_shiny;
        this.detail["abilities"] = pokedexAPI.abilities;
        this.detail["types"] = pokedexAPI.types;
        this.detail["stats"] = pokedexAPI.stats;
        this.loading = false
        console.log(this.detail)
        return;
    }
};
</script>

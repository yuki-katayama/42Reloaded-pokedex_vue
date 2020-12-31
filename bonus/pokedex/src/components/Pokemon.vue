<template>
<div>
    <div class="all-box">
        <router-link :to="{name: 'pokedex'}" class="back">ポケモン選択に戻る</router-link>
        <h1 v-if="errored" class="error">{{Errmassage}}</h1>
        <div v-else>
            <h1 v-if="loading">Loading...</h1>
            <div v-else>
                <div class="id">
                    <h2>ID: {{detail.id}}</h2>
                </div>
                <div class="graph">
                    <chart style="height: 300px; width: 300px;" :statsData="statsData"></chart>
                </div>
                <div class="name">
                    <p>{{detail.name}}</p>
                </div>
                <div class="image">
                    <p>
                        <img v-bind:src="detail.image" height="280">
                    </p>
                </div>
                <br>
                <div class="abilities">
                    <table>
                        <tr>
                    <th>技</th>
                    </tr>
                    <div v-for="(abilities) in detail.abilities" :key="abilities.id">
                        <tr>
                        <td>{{abilities.ability.name}}</td>
                        </tr>
                    </div>
                    </table>

                </div>
                <br>
                <div class="type">
                        <table>
                            <tr>
                    <th>タイプ: </th>
                    </tr>
                    <div v-for="(type) in detail.types" :key="type.id">
                        <tr>
                        <td>{{type.type.name}}</td>
                            </tr>
                    </div>
                        </table>
                </div>
            </div>
        </div>
    </div>
    <div class="background-container">
        <img src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/1231630/moon2.png" alt="" width="350">
    </div>
    <div class="stars"></div>
    <div class="twinkling"></div>
    <div class="clouds"></div>
</div>
</template>

<script>
import axios from 'axios';
import Chart from './Chart';

export default {
    props: {
        pokemonData: {
            type: Object,
            //   require: true
        }
    },
    components: {
        Chart,
    },
    data: function () {
        return {
            detail: {},
            statsData: [],
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
        for (let i = 0; i < this.detail["stats"].length; i++) {
            this.statsData.push(this.detail["stats"][i].base_stat);
        }
        this.loading = false
        console.log(this.statsData)
        return;
    }
};
</script>

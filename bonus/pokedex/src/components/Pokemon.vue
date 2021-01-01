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
                <div class="location">
                    <table>
                        <tr>
                            <th>生息地</th>
                            <!-- </tr> -->
                            <td v-for="(location) in detail.location_area" :key=location.id>
                                <!-- <tr> -->
                                <p>{{location}}</p>
                            </td>
                        </tr>
                    </table>
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
                                <td>
                                    {{abilities.ability.name}}
                                </td>
                            </tr>
                        </div>
                    </table>

                </div>
                <br>
                <div class="type">
                    <table>
                        <tr>
                            <th>タイプ</th>
                        </tr>
                        <div v-for="(type) in detail.types" :key="type.id">
                            <tr>
                                <td>{{type.type.name}}</td>
                            </tr>
                        </div>
                        <tr>
                            <td>{{detail.pokeType}}</td>
                        </tr>
                    </table>
                </div>
                <div class="text">
                    <span class="poke-type-content">{{detail.text}}</span>
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
import Chart from './Pokemon_app/PokemonChart';
import './Pokemon_app/Pokemon.css'

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
            pokedexAPI: {},
            detail: {},
            statsData: [],
            Errmassage: '',
            loading: true,
            errored: false,
        };
    },
    methods: {
        async api1() {

        },
        async api2() {}
    },
    computed: {},
    mounted: async function () {
        let api_tmp;
        //基本ステータス取得
        await axios.get('https://pokeapi.co/api/v2/pokemon/' + this.pokemonData.id)
            .then(response => this.pokedexAPI = response.data)
            .catch(error => {
                this.Errmassage = error
                this.errored = true
            })
        if (this.errored) {
            return
        }
        for (let i = 0; i < this.pokedexAPI.length; i++) {
            this.url.push(this.pokedexAPI[i].url)
        }
        this.detail["id"] = this.pokedexAPI.id;
        this.detail["image"] = this.pokedexAPI.sprites.front_shiny;
        this.detail["abilities"] = this.pokedexAPI.abilities;
        this.detail["types"] = this.pokedexAPI.types;
        this.detail["stats"] = this.pokedexAPI.stats;
        for (let i = 0; i < this.detail["stats"].length; i++) {
            this.statsData.push(this.detail["stats"][i].base_stat);
        }

        //生息地取得
        await axios.get(this.pokedexAPI.location_area_encounters)
            .then(response => api_tmp = response.data)
            .catch(error => {
                this.Errmassage = error
                this.errored = true
            })
        if (this.errored) {
            return
        }
        this.detail["location_area"] = {}
        if (api_tmp[0] == undefined) {
            this.detail["location_area"][0] = "なし";
        } else {
            for (let i = 0; i < (api_tmp.length); i++) {
                this.detail["location_area"][i] = api_tmp[i].location_area.name;
                if (i == 2)
                    break;
            }
        }
        // このポケモンの詳細をゲット
        console.log(this.pokedexAPI.species.url)
        await axios.get(this.pokedexAPI.species.url)
            .then(response => api_tmp = response.data)
            .catch(error => {
                this.Errmassage = error
                this.errored = true
            })
        if (this.errored) {
            return
        }
        console.log(api_tmp)
        this.detail["name"] = api_tmp.names[0].name;
        //説明テキストを取得
        for(let i = 0; i < api_tmp.flavor_text_entries.length; i++)
        {
            if(api_tmp.flavor_text_entries[i].language.name == "ja")
            {
                this.detail["text"] = api_tmp.flavor_text_entries[i].flavor_text
                break;
            }
        }
        //ポケモンの種類を取得
        for(let i = 0; i < api_tmp.genera.length; i++)
        {
            if(api_tmp.genera[i].language.name == "ja")
            {
                this.detail["pokeType"] = api_tmp.genera[i].genus
                break;
            }
        }
        this.detail["text"] = this.detail["text"].replaceAll("　", "")
        // console.log(this.detail["poke-type"])
        this.loading = false;
    }
};
</script>

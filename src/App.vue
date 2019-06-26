<template>
    <div id="app">
        <h2>Array of strings</h2>
        <div class="options">
            <p><b>Options:</b></p>
            <div>
                <input type="checkbox" v-model="disabledName">
                disabled
            </div>
            <div>
                <input type="checkbox" v-model="searchableName">
                searchable
            </div>
        </div>
        <VSelect
                :disabled="disabledName"
                :options="names"
                :searchable="searchableName"
                v-model="selectedName"/>
        <h2>Array of objects</h2>
        <div class="options">
            <p><b>Options:</b></p>
            <div>
                <input type="checkbox" v-model="disabledContinent">
                disabled
            </div>
            <div>
                <input type="checkbox" v-model="searchableContinent">
                searchable
            </div>
        </div>
        <VSelect
                :disabled="disabledContinent"
                :options="continents"
                :searchable="searchableContinent"
                disabled-prop="inactive"
                v-model="selectedContinent"/>

        <h2>Asynchronous provider function</h2>
        <div class="options">
            <p><b>Options:</b></p>
            <div>
                <input type="checkbox" v-model="disabledProviderAsync">
                disabled
            </div>
            <div>
                <input type="checkbox" v-model="searchableProviderAsync">
                searchable
            </div>
        </div>
        <VSelect
                :disabled="disabledProviderAsync"
                :options="biggestEuropeanCities"
                :searchable="searchableProviderAsync"
                disabled-prop="inactive"
                text-prop="name"
                value-prop="population"
                v-model="selectedProviderAsync"/>

        <h2>Synchronous provider function</h2>
        <div class="options">
            <p><b>Options:</b></p>
            <div>
                <input type="checkbox" v-model="disabledProviderSync">
                disabled
            </div>
            <div>
                <input type="checkbox" v-model="searchableContinent">
                searchable
            </div>
        </div>
        <VSelect
                :disabled="disabledProviderSync"
                :options="biggestEuropeanCitiesSync"
                :searchable="searchableProviderSync"
                disabled-prop="inactive"
                text-prop="name"
                value-prop="population"
                v-model="selectedProviderSync"/>

        <h2>Templated label</h2>
        <div class="options">
            <p><b>Options:</b></p>
            <div>
                <input type="checkbox" v-model="disabledProviderSync">
                disabled
            </div>
            <div>
                <input type="checkbox" v-model="searchableContinent">
                searchable
            </div>
        </div>
        <VSelect
                :disabled="disabledUser"
                :options="users"
                :searchable="searchableUser"
                disabled-prop="inactive"
                text-prop="name"
                value-prop="population"
                v-model="selectedUser">
            <template slot="label" slot-scope="data">
                <img class="display-inline border-radius-50 img-fit" :src="data.option.picture">
                <span>
                    {{data.option.name}}
                </span>
            </template>
        </VSelect>
    </div>

</template>

<script>
    import VSelect from "@/vue-bootstrap-select";

    export default {
        name: "App",
        components: {
            VSelect
        },
        data() {
            return {
                selectedName: null,
                searchableName: false,
                disabledName: false,
                names: [
                    "John Doe",
                    "Olive Yew",
                    "Aida Bugg",
                    "Teri Dactyl",
                    "Paige Turner"
                ],

                continents: [
                    {value: 0, text: "Africa"},
                    {value: 1, text: "America"},
                    {value: 2, text: "Asia"},
                    {value: 3, text: "Europe"},
                    {value: 4, text: "Oceania"},
                    {value: 5, text: "Antartica"}
                ],

                selectedContinent: null,
                searchableContinent: false,
                disabledContinent: false,

                cities: [
                    {
                        name: "Istanbul",
                        population: 15029231
                    },
                    {
                        name: "Moscow",
                        population: 12615279
                    },
                    {
                        name: "London",
                        population: 9126366
                    },
                    {
                        name: "Saint Petersburg",
                        population: 5383890
                    },
                    {
                        name: "Berlin",
                        population: 3748148
                    },
                    {
                        name: "Madrid",
                        population: 3223334
                    },
                    {
                        name: "Kyiv",
                        population: 2950819
                    },
                    {
                        name: "Rome",
                        population: 2857321
                    },
                    {
                        name: "Paris",
                        population: 2140526
                    }
                ],

                selectedProviderAsync: null,
                searchableProviderAsync: false,
                disabledProviderAsync: false,

                selectedProviderSync: null,
                searchableProviderSync: false,
                disabledProviderSync: false,

                users: [
                    {
                        id: 1,
                        name: "Brian",
                        picture: "/images/profilepictures/brian.jpeg",
                    },
                    {
                        id: 2,
                        name: "Charlotte",
                        picture: "/images/profilepictures/charlotte.jpeg",
                    },
                    {
                        id: 3,
                        name: "Leo",
                        picture: "/images/profilepictures/leo.jpeg",
                    },
                    {
                        id: 4,
                        name: "Lisa",
                        picture: "/images/profilepictures/lisa.jpeg",
                    },
                    {
                        id: 5,
                        name: "Robert",
                        picture: "/images/profilepictures/robert.jpeg",
                    },

                ],

                selectedUser: null,
                searchableUser: false,
                disabledUser: false,
            };
        },

        methods: {
            biggestEuropeanCities: function (ctx) {
                return new Promise(res => {
                    res(this.cities.filter(c => c.name.indexOf(ctx.search) > -1));
                });
            },
            biggestEuropeanCitiesSync: function (ctx) {
                return this.cities.filter(c => c.name.indexOf(ctx.search) > -1);
            }
        }
    };
</script>


<style lang="css" scoped>
    #app {
        font-family: "Roboto", Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        padding: 2em 1em;
    }

    .options {
        text-align: left;
        padding: 1em 0em;
    }


    .display-inline {
    }

    .border-radius-50 {
        -webkit-border-radius: 50%;
        -moz-border-radius: 50%;
        border-radius: 50%;
    }

    .img-fit {
        max-height: 3em;
        width: auto;
    }
</style>

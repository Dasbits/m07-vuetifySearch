<template>
    <v-layout class="rounded fill-height rounded-md">
        <v-app-bar title="Cercador de Pel·licules"></v-app-bar>

        <v-navigation-drawer>
            <v-list>
                <v-list-item title="Navigation drawer"></v-list-item>
            </v-list>
        </v-navigation-drawer>

        <v-main class="">
            <!-- Formulario de búsqueda -->
            <v-row class="mt-5" justify="center">
                <v-col cols="12" md="6">
                    <v-text-field v-model="searchQuery" label="introdueix el nom de la pel·licula" outlined clearable />
                </v-col>
                <v-col cols="12" md="3">
                    <v-btn color="primary" block @click="searchMovies" :loading="isLoading">
                        Cerca pel·licula
                        <template v-slot:loader>
                            <v-progress-linear indeterminate></v-progress-linear>
                        </template>
                    </v-btn>
                </v-col>
            </v-row>

            <!-- Resultados de búsqueda -->
            <v-row v-if="movies.length" class="mt-5 ml-1 mr-1">
                <v-col v-for="movie in movies" :key="movie.imdbID" cols="12" sm="6" md="4">
                    <v-card>
                        <v-img :src="movie.Poster" aspect-ratio="1.7" />
                        <v-card-title>{{ movie.Title }}</v-card-title>
                        <v-card-subtitle>{{ movie.Year }}</v-card-subtitle>
                        <v-card-actions>
                            <v-btn text :href="`https://www.imdb.com/title/${movie.imdbID}`" target="_blank">
                                Ver en IMDB
                            </v-btn>
                        </v-card-actions>
                    </v-card>
                </v-col>
            </v-row>

            <!-- Mensaje de error -->
            <v-row v-if="error" class="mt-5" justify="center">
                <v-col cols="12" md="6">
                    <v-alert type="error" outlined>{{ error }}</v-alert>
                </v-col>
            </v-row>
        </v-main>
    </v-layout>
</template>

<script setup>
import { ref } from 'vue';

const searchQuery = ref('');
const movies = ref([]);
const isLoading = ref(false);
const error = ref('');

const searchMovies = async () => {
    if (!searchQuery.value.trim()) {
        error.value = 'Introdueix una búsqueda.';
        return;
    }
    error.value = '';
    movies.value = [];
    isLoading.value = true;

    try {
        const apiKey = '19f8a30e';
        const response = await fetch(
            `http://www.omdbapi.com/?s=${encodeURIComponent(searchQuery.value)}&apikey=${apiKey}`
        );
        const data = await response.json();
        if (data.Response === 'True') {
            movies.value = data.Search;
        } else {
            error.value = data.Error;
        }
    } catch (e) {
        error.value = 'Error al fer la búsqueda.';
    } finally {
        isLoading.value = false;
    }
};
</script>
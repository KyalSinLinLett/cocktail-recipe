<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Random Recipe</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content v-if="state.loading">
      <div class="loading-center">
        <ion-spinner color="primary"></ion-spinner>
      </div>
    </ion-content>
    <ion-content :fullscreen="true" v-else>
      <ion-refresher slot="fixed" @ionRefresh="doRefresh">
        <ion-refresher-content> </ion-refresher-content>
      </ion-refresher>
      <ion-card>
        <img :src="state.randomCocktail.strDrinkThumb" />
        <ion-card-header>
          <ion-card-subtitle>
            {{ state.randomCocktail.strCategory }} | Served in
            {{ state.randomCocktail.strGlass }}
          </ion-card-subtitle>
          <ion-card-title>
            {{ state.randomCocktail.strDrink }}
          </ion-card-title>
        </ion-card-header>
        <ion-card-content>
          <p>{{ state.randomCocktail.strInstructions }}</p>
          <ion-list-header>Ingredients</ion-list-header>
          <ion-list>
            <ion-item
              v-for="num in 15"
              :key="num"
              v-show="state.randomCocktail['strIngredient' + num]"
            >
              <ion-label>
                <span v-if="state.randomCocktail['strMeasure' + num]">
                  {{ state.randomCocktail["strMeasure" + num] }} -
                </span>
                {{ state.randomCocktail["strIngredient" + num] }}
              </ion-label>
            </ion-item>
          </ion-list>
        </ion-card-content>
      </ion-card>
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import { reactive } from "vue";
import axios from "axios";
import { ServerResponse } from "@/model/cocktail";

import {
  IonPage,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
  IonSpinner,
  IonRefresher,
  IonRefresherContent,
  IonCard,
  IonCardSubtitle,
  IonCardContent,
  IonItem,
  IonList,
  IonListHeader,
  IonLabel,
  IonCardTitle,
  IonCardHeader,
} from "@ionic/vue";

export default {
  name: "Tab1",
  components: {
    IonHeader,
    IonToolbar,
    IonTitle,
    IonContent,
    IonPage,
    IonSpinner,
    IonRefresher,
    IonRefresherContent,
    IonItem,
    IonList,
    IonListHeader,
    IonLabel,
    IonCard,
    IonCardTitle,
    IonCardSubtitle,
    IonCardContent,
    IonCardHeader,
  },

  setup() {
    const state = reactive({
      randomCocktail: {},
      loading: false,
    });

    const fetchRandomCocktail = async (displayLoaderPage: boolean) => {
      if (displayLoaderPage) {
        state.loading = true;
      }

      axios
        .request<string>({
          url: "https://www.thecocktaildb.com/api/json/v1/1/random.php",
          transformResponse: (r: ServerResponse) => r,
        })
        .then((response) => {
          const data = { drinks: response.data };
          const drinks = JSON.parse(data.drinks).drinks[0];

          state.randomCocktail = drinks;
          state.loading = false;
        });
    };

    const doRefresh = (event: any) => {
      fetchRandomCocktail(false);

      event.target.complete();
    };

    fetchRandomCocktail(true);

    return {
      state,
      fetchRandomCocktail,
      doRefresh,
    };
  },
};
</script>

<style scoped>
.loading-center {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 90vh;
}

ion-spinner {
  transform: scale(1.5);
}
</style>
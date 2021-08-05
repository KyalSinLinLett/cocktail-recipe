<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-buttons slot="start">
          <ion-back-button>Back</ion-back-button>
        </ion-buttons>
        <ion-title size="medium">Drink</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content v-if="state.loading">
      <div class="loading-center">
        <ion-spinner color="primary"></ion-spinner>
      </div>
    </ion-content>
    <ion-item v-if="state.error">
      <ion-label>Drink not found...</ion-label>
    </ion-item>
    <ion-content :fullscreen="true" v-else>
      <ion-card>
        <img :src="state.cocktail.strDrinkThumb" />
        <ion-card-header>
          <ion-card-subtitle>
            {{ state.cocktail.strCategory }} | Served in
            {{ state.cocktail.strGlass }}
          </ion-card-subtitle>
          <ion-card-title>
            {{ state.cocktail.strDrink }}
          </ion-card-title>
        </ion-card-header>
        <ion-card-content>
          <p>{{ state.cocktail.strInstructions }}</p>
          <ion-list-header>Ingredients</ion-list-header>
          <ion-list>
            <ion-item
              v-for="num in 15"
              :key="num"
              v-show="state.cocktail['strIngredient' + num]"
            >
              <ion-label>
                <span v-if="state.cocktail['strMeasure' + num]">
                  {{ state.cocktail["strMeasure" + num] }} -
                </span>
                {{ state.cocktail["strIngredient" + num] }}
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
import { useRoute } from "vue-router";
import axios from "axios";
import { ServerResponse } from "@/model/cocktail";

import {
  IonPage,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
  IonSpinner,
  IonCard,
  IonCardSubtitle,
  IonCardContent,
  IonItem,
  IonList,
  IonListHeader,
  IonLabel,
  IonCardTitle,
  IonCardHeader,
  IonBackButton,
  IonButtons,
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
    IonItem,
    IonList,
    IonListHeader,
    IonLabel,
    IonCard,
    IonCardTitle,
    IonCardSubtitle,
    IonCardContent,
    IonCardHeader,
    IonBackButton,
    IonButtons,
  },

  setup() {
    const state = reactive({
      cocktail: {},
      loading: false,
      error: false,
    });

    const route = useRoute();

    const drinkId = route.params.drinkId as string;

    const fetchCocktail = async (displayLoaderPage: boolean) => {
      if (displayLoaderPage) {
        state.loading = true;
      }

      axios
        .request<string>({
          url: `https://www.thecocktaildb.com/api/json/v1/1/lookup.php?i=${drinkId}`,
          transformResponse: (r: ServerResponse) => r,
        })
        .then((response) => {
          const data = { drinks: response.data };
          const drinks = JSON.parse(data.drinks).drinks[0];

          state.cocktail = drinks;
          state.loading = false;
          state.error = false;
        })
        .catch((_) => {
          /* eslint-disable */
          state.error = true;
        });
    };

    const doRefresh = (event: any) => {
      fetchCocktail(false);

      event.target.complete();
    };

    fetchCocktail(true);

    return {
      state,
      fetchCocktail,
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
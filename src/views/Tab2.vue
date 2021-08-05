<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Search</ion-title>
      </ion-toolbar>
      <ion-searchbar
        debounce="500"
        :onIonChange="(e) => fetchRecipe(e.detail.value)"
        placeholder="i.e Vodka"
      ></ion-searchbar>
    </ion-header>
    <ion-content v-if="state.loading">
      <div class="loading-center">
        <ion-spinner color="primary"></ion-spinner>
      </div>
    </ion-content>
    <ion-content :fullscreen="true" v-else>
      <ion-item v-if="state.error">
        <ion-label>Drink not found...</ion-label>
      </ion-item>
      <ion-card
        v-show="!state.error"
        v-for="rec in state.lstIngredients"
        :key="rec"
        @click="() => router.push(`/drink/${rec.idDrink}`)"
      >
        <img :src="rec.strDrinkThumb" />
        <ion-card-header>
          <ion-card-title>
            {{ rec.strDrink }}
          </ion-card-title>
          <ion-card-subtitle>Tap for recipe...</ion-card-subtitle>
        </ion-card-header>
      </ion-card>
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import { reactive } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";
import {
  IonPage,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
  IonSearchbar,
  IonCard,
  IonCardTitle,
  IonCardSubtitle,
  IonCardHeader,
  IonItem,
  IonLabel,
  IonSpinner,
} from "@ionic/vue";

export default {
  name: "Tab2",
  components: {
    IonHeader,
    IonToolbar,
    IonTitle,
    IonContent,
    IonPage,
    IonSearchbar,
    IonCard,
    IonCardTitle,
    IonCardSubtitle,
    IonCardHeader,
    IonItem,
    IonLabel,
    IonSpinner,
  },

  setup() {
    const router = useRouter();
    const state = reactive({
      lstIngredients: [],
      loading: false,
      error: false,
    });

    const fetchRecipe = async (searchTerm: string) => {
      if (searchTerm) {
        state.loading = true;

        axios
          .request<any>({
            url: `https://www.thecocktaildb.com/api/json/v1/1/filter.php?i=${searchTerm}`,
            transformResponse: (r: any) => r,
          })
          .then((response) => {
            const data = { drinks: response.data };
            state.lstIngredients = JSON.parse(data.drinks).drinks;
            state.loading = false;
            state.error = false;
          })
          .catch((_) => {
            /* eslint-disable */
            state.error = true;
            state.loading = false;
          });
      }
    };

    return {
      state,
      router,
      fetchRecipe,
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
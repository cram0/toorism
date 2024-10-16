<script setup lang="ts">
import type { PropType } from "vue";
import { ref, computed, watch, onMounted } from "vue";

const emits = defineEmits(["close", "like"]);

const props = defineProps({
  destination: {
    type: Object as PropType<Destination>,
    required: true,
  },
  display: {
    type: Boolean,
    required: true,
  },
});

const showFullDescription = ref<boolean>(false);
const ticketCount = ref<number>(1);

const totalPrice = computed(() => (props.destination.price * ticketCount.value).toFixed(2));

const closePage = () => {
  showFullDescription.value = false;
  ticketCount.value = 1;
  emits("close");
};

const likeDestination = () => {
  emits("like", props.destination.id);
};

// Open the destination on OpenStreetMap
const openMap = () => {
  window.open(`https://www.openstreetmap.org/#map=17/${props.destination.lat}/${props.destination.lng}`);
};

// Limit the ticket count between 1 and 99
watch(
  () => ticketCount.value,
  (newCount: number) => {
    if (newCount < 1) {
      ticketCount.value = 1;
    }
    if (newCount > 99) {
      ticketCount.value = 99;
    }
  },
);

// Whenever we display the page, we disable the scroll on the body to avoid scrolling the page behind the modal
watch(
  () => props.display,
  (display: boolean) => {
    if (display) {
      document.body.style.overflow = "hidden";
    } else {
      document.body.style.overflow = "auto";
    }
  },
  { immediate: true },
);

// Close the page when pressing the Escape key
onMounted(() => {
  addEventListener("keydown", (e) => {
    if (e.key === "Escape") {
      closePage();
    }
  });
});
</script>

<template>
  <div @click.self="closePage()" class="fixed inset-0 z-10 p-4 backdrop-blur-md transition-all ease-in" :class="props.display ? 'bg-black/50' : 'invisible bg-black/0'" aria-label="Vous pouvez fermer cette page en cliquant sur la touche Escape.">
    <div class="relative mx-auto h-full overflow-auto rounded-lg bg-white transition-all ease-in md:max-w-7xl" :class="props.display ? 'translate-y-0' : 'invisible translate-y-full'">
      <div class="relative">
        <button @click.prevent="closePage()" class="absolute right-4 top-4 rounded-full bg-white p-2">
          <svg class="h-6 w-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
          </svg>
        </button>
        <img class="h-48 w-full object-cover" :src="props.destination.image" />
      </div>
      <div class="flex flex-col gap-4 divide-y p-4">
        <div class="flex flex-col gap-4">
          <div>
            <h1 class="text-2xl font-bold">{{ props.destination.title }}</h1>
            <p class="text-gray-400">{{ props.destination.city }}, {{ props.destination.country }}</p>
          </div>
          <div>
            <p v-if="props.destination.description.length < 100" class="text-base italic">{{ props.destination.description }}</p>
            <p v-else class="text-base italic">{{ showFullDescription ? props.destination.description : props.destination.description.slice(0, 100) + "..." }}</p>
            <div v-if="props.destination.description.length >= 100">
              <button class="text-orange-400 active:text-orange-300" v-if="showFullDescription === false" @click.prevent="showFullDescription = true">Show more</button>
              <button class="text-orange-400 active:text-orange-300" v-else @click="showFullDescription = false">Show less</button>
            </div>
          </div>
          <button class="rounded border p-2 font-light text-gray-400" @click.prevent="openMap()">Voir sur OpenStreetMap</button>
          <!-- ticket count + pay -->
          <div class="flex flex-col sm:flex-row">
            <div class="flex basis-1/2 flex-col">
              <div class="p-2">
                <!-- TODO: Map zoom fix ? -->
                <iframe class="h-64 w-full" :src="'https://www.openstreetmap.org/export/embed.html?bbox=' + props.destination.lng + '%2C' + props.destination.lat + '%2C' + props.destination.lng + '%2C' + props.destination.lat + '&amp;layer=mapnik&amp;marker=' + props.destination.lat + '%2C' + props.destination.lng"></iframe>
              </div>
            </div>
            <div class="flex basis-1/2 flex-col justify-between gap-2 p-2">
              <div>
                <div class="flex flex-row items-center justify-between gap-2">
                  <div class="flex flex-row gap-2">
                    <p class="font-semibold">Plein tarif</p>
                    <p class="font-light">- {{ props.destination.price }} €</p>
                  </div>
                  <select class="rounded border p-1" v-model="ticketCount">
                    <option v-for="i in 10" :key="i">{{ i }}</option>
                  </select>
                </div>
              </div>
              <div class="flex flex-col gap-2">
                <div class="flex flex-row justify-between">
                  <p class="text-xl font-semibold">Total</p>
                  <p class="font-toorism text-2xl font-bold">{{ totalPrice }} €</p>
                </div>
                <button class="w-full rounded bg-orange-400 p-4 text-xl font-bold text-white active:bg-orange-300">Ajouter au panier</button>
              </div>
            </div>
          </div>
        </div>
        <div class="flex w-full flex-row justify-around p-4">
          <div class="flex basis-1/2 flex-col items-center justify-center gap-1">
            <div>
              <button class="rounded-full border p-2" @click.prevent="likeDestination()">
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6 transition-colors" :class="props.destination.liked ? 'fill-red-500 text-red-500' : 'fill-white text-gray-400'">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M21 8.25c0-2.485-2.099-4.5-4.688-4.5-1.935 0-3.597 1.126-4.312 2.733-.715-1.607-2.377-2.733-4.313-2.733C5.1 3.75 3 5.765 3 8.25c0 7.22 9 12 9 12s9-4.78 9-12Z" />
                </svg>
              </button>
            </div>
            <p class="text-center text-xs">{{ props.destination.liked ? "J'aime déjà" : "J'aime" }}</p>
          </div>
          <div class="flex basis-1/2 flex-col items-center justify-center gap-1">
            <div>
              <button class="rounded-full border p-2">
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6 text-gray-400">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M7.217 10.907a2.25 2.25 0 1 0 0 2.186m0-2.186c.18.324.283.696.283 1.093s-.103.77-.283 1.093m0-2.186 9.566-5.314m-9.566 7.5 9.566 5.314m0 0a2.25 2.25 0 1 0 3.935 2.186 2.25 2.25 0 0 0-3.935-2.186Zm0-12.814a2.25 2.25 0 1 0 3.933-2.185 2.25 2.25 0 0 0-3.933 2.185Z" />
                </svg>
              </button>
            </div>
            <p class="text-center text-xs">Partager</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

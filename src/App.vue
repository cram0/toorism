<script setup lang="ts">
import { ref } from "vue";
import DestinationCard from "./components/DestinationCard.vue";

const destinations = ref<Destination[]>([
  {
    id: 0,
    title: "District d'Akihabara",
    city: "Tokyo",
    country: "Japan",
    description: "Le district d'Akihabara est un quartier de Tokyo, au Japon, connu pour ses nombreux magasins d'électronique, ses cafés à thème et ses animations de rue.",
    image: "https://cdn.cheapoguides.com/wp-content/uploads/sites/2/2020/05/akihabara-iStock-484915982-1024x600.jpg",
    price: 8,
    avgRating: 4.5,
    liked: true,
    lat: 35.702258,
    lng: 139.774472,
  },
  {
    id: 1,
    title: "Eiffel Tower",
    city: "Paris",
    country: "France",
    description: "La tour Eiffel est une tour de fer puddlé de 330 mètres de hauteur située à Paris, à lextrémité nord-ouest du parc du Champ-de-Mars en bordure de la Seine dans le 7ᵉ arrondissement.",
    image: "https://www.toureiffel.paris/themes/custom/tour_eiffel/img/tour-eiffel-paris.jpg",
    price: 18.99,
    avgRating: 4.8,
    liked: false,
    lat: 48.8584,
    lng: 2.2945,
  },
  {
    id: 2,
    title: "Statue de la Liberté",
    city: "New York",
    country: "United States",
    description: "La statue de la Liberté est une sculpture néo-classique sur Liberty Island dans le port de New York, dans la ville et l'État de New York aux États-Unis. L'œuvre a été offerte par le peuple français aux Américains en 1886 pour célébrer le centenaire de la Déclaration d'indépendance des États-Unis et en signe d'amitié entre les deux nations.",
    image: "https://cdn.britannica.com/61/93061-050-99147DCE/Statue-of-Liberty-Island-New-York-Bay.jpg",
    price: 28.49,
    avgRating: 3.7,
    liked: true,
    lat: 40.6892,
    lng: -74.0445,
  },
  {
    id: 3,
    title: "Parc de Lunaret",
    city: "Montpellier",
    country: "France",
    description: "Le parc zoologique de Montpellier est un parc zoologique français situé dans le parc de Lunaret, à Montpellier, dans le département de l'Hérault. Il est géré par la ville de Montpellier et s'étend sur 80 hectares. Le parc zoologique de Montpellier est l'un des plus grands parcs zoologiques de France et abrite plus de 750 animaux de 140 espèces différentes.",
    image: "https://dynamic-media-cdn.tripadvisor.com/media/photo-o/06/0b/94/32/parc-zoologique-henri.jpg?w=800&h=-1&s=1",
    price: 12.99,
    avgRating: 4.2,
    liked: false,
    lat: 43.64222,
    lng: 3.87908,
  },
  {
    id: 4,
    title: "Cathédrale Saint-Pierre",
    city: "Montpellier",
    country: "France",
    description: "La cathédrale Saint-Pierre de Montpellier est une cathédrale catholique romaine située à Montpellier, dans le département de l'Hérault, en France. Elle est le siège de l'archidiocèse de Montpellier. La cathédrale est un exemple de l'architecture gothique méridionale.",
    image: "https://dynamic-media-cdn.tripadvisor.com/media/photo-o/0d/a5/90/60/ingresso.jpg?w=2000&h=-1&s=1",
    price: 5.99,
    avgRating: 4.6,
    liked: false,
    lat: 43.613281,
    lng: 3.874014,
  },
  {
    id: 5,
    title: "Musée Fabre",
    city: "Montpellier",
    country: "France",
    description: "La collection du Musée Fabre abrite des peintures et des céramiques du 15e au 19e siècle. De nombreux artistes français de renom sont représentés dont Poussin, Jacques-Louis David, et Courbet. Le musée est situé au centre de la ville et est facilement accessible à pied ou Segway.",
    image: "https://dynamic-media-cdn.tripadvisor.com/media/photo-o/17/68/68/17/photo0jpg.jpg?w=1400&h=-1&s=1",
    price: 9.99,
    avgRating: 4.1,
    liked: false,
    lat: 43.611749,
    lng: 3.879747,
  },
  {
    id: 6,
    title: "Place de la Comédie",
    city: "Montpellier",
    country: "France",
    description: "Vous ne manquerez pas de passer par la Place de la Comédie lors de vos déplacements à Montpellier. La place centrale est agréable à explorer dans le cadre d'une visite guidée, où un guide local vous fera découvrir l'histoire de la place et de sa célèbre statue des Trois Grâces.",
    image: "https://dynamic-media-cdn.tripadvisor.com/media/photo-o/13/17/0e/28/20180527-091325-2-largejpg.jpg?w=2200&h=-1&s=1",
    price: 3.99,
    avgRating: 4.3,
    liked: false,
    lat: 43.608704,
    lng: 3.880055,
  },
]);

// Define the destination to show in the destination page after clicking on one destination
const activeDestination = ref<Destination>(destinations.value[0]);

const displayDestinationPage = ref<boolean>(false);

const setActiveDestination = (destination: Destination) => {
  activeDestination.value = destination;
};

const closeDestinationPage = () => {
  displayDestinationPage.value = false;
};

const openDestinationPage = (destination: Destination) => {
  setActiveDestination(destination);
  displayDestinationPage.value = true;
};

const toggleLike = (id: number) => {
  const destination = destinations.value.find((d) => d.id === id);
  if (destination) {
    destination.liked = !destination.liked;
  }
};

// Get random destinations for the "D'autres personnes ont aussi aimé" section
const getRandomDestinations = (count: number) => {
  const randomDestinations: Destination[] = [];
  const randomIndexList: number[] = [];
  while (randomIndexList.length < count) {
    const randomIndex = Math.floor(Math.random() * destinations.value.length);
    if (!randomIndexList.includes(randomIndex)) {
      randomIndexList.push(randomIndex);
      randomDestinations.push(destinations.value[randomIndex]);
    }
  }
  return randomDestinations;
};
</script>

<template>
  <DestinationCard :destination="activeDestination" :display="displayDestinationPage" @close="closeDestinationPage()" @like="(id: number) => toggleLike(id)" />
  <div class="flex h-full flex-col gap-4 bg-orange-50 p-4">
    <!-- Title -->
    <div class="flex flex-row items-center align-middle">
      <h1 class="truncate font-toorism text-6xl font-bold text-orange-400">T</h1>
      <svg xmlns="http://www.w3.org/2000/svg" class="text-orange-300" width="80" height="80" fill="currentColor" viewBox="0 0 16 16">
        <path d="M3 5a2 2 0 0 0-2 2v.5H.5a.5.5 0 0 0 0 1H1V9a2 2 0 0 0 2 2h1a3 3 0 0 0 3-3 1 1 0 1 1 2 0 3 3 0 0 0 3 3h1a2 2 0 0 0 2-2v-.5h.5a.5.5 0 0 0 0-1H15V7a2 2 0 0 0-2-2h-2a2 2 0 0 0-1.888 1.338A2 2 0 0 0 8 6a2 2 0 0 0-1.112.338A2 2 0 0 0 5 5zm0 1h.941c.264 0 .348.356.112.474l-.457.228a2 2 0 0 0-.894.894l-.228.457C2.356 8.289 2 8.205 2 7.94V7a1 1 0 0 1 1-1" />
      </svg>
      <h1 class="truncate font-toorism text-6xl font-bold text-orange-400">rism</h1>
    </div>
    <!-- Recommended -->
    <div class="flex flex-col gap-2">
      <p class="font-toorism text-2xl font-semibold text-orange-400">Recommandé pour vous</p>
      <div class="rounded-lg bg-orange-100 p-2">
        <div class="flex flex-col flex-wrap sm:flex-row">
          <div v-for="destination in getRandomDestinations(4)" :key="destination.id" class="p-2 sm:w-1/2 md:w-1/3 xl:w-1/4">
            <button @click.prevent="openDestinationPage(destination)" class="flex w-full flex-row overflow-hidden rounded-lg bg-orange-50 text-orange-600 shadow-md sm:flex-col">
              <img class="h-32 w-1/5 shrink-0 object-cover sm:h-32 sm:w-full" :src="destination.image" />
              <div class="flex w-full flex-col items-start p-4 sm:p-2">
                <p class="truncate text-ellipsis text-xl font-bold">{{ destination.title }}</p>
                <p class="truncate text-sm text-orange-400">{{ destination.city }}, {{ destination.country }}</p>
                <div class="flex w-full flex-row items-end justify-between">
                  <div class="flex flex-row gap-1">
                    <p class="font-toorism font-light text-black">{{ destination.avgRating }}</p>
                    <p class="text-yellow-500">★</p>
                  </div>
                  <div class="flex flex-col items-end">
                    <p class="text-xs text-orange-300">À partir de</p>
                    <p class="font-toorism text-xl font-semibold">
                      {{ destination.price + " €" }}
                    </p>
                  </div>
                </div>
              </div>
            </button>
          </div>
        </div>
      </div>
    </div>
    <!-- Other people liked -->
    <div class="flex flex-col gap-2">
      <p class="font-toorism text-2xl font-semibold text-orange-400">D'autres personnes ont aussi aimé</p>
      <div class="rounded-lg bg-orange-100 p-2">
        <div class="flex flex-col flex-wrap sm:flex-row">
          <div v-for="destination in getRandomDestinations(3)" :key="destination.id" class="p-2 sm:w-1/2 md:w-1/3 xl:w-1/4">
            <button @click.prevent="openDestinationPage(destination)" class="flex w-full flex-row overflow-hidden rounded-lg bg-orange-50 text-orange-600 shadow-md sm:flex-col">
              <img class="h-32 w-1/5 shrink-0 object-cover sm:h-32 sm:w-full" :src="destination.image" />
              <div class="flex w-full flex-col items-start p-4 sm:p-2">
                <p class="truncate text-ellipsis text-xl font-bold">{{ destination.title }}</p>
                <p class="truncate text-sm text-orange-400">{{ destination.city }}, {{ destination.country }}</p>
                <div class="flex w-full flex-row items-end justify-between">
                  <div class="flex flex-row gap-1">
                    <p class="font-toorism font-light text-black">{{ destination.avgRating }}</p>
                    <p class="text-yellow-500">★</p>
                  </div>
                  <div class="flex flex-col items-end">
                    <p class="text-xs text-orange-300">À partir de</p>
                    <p class="font-toorism text-xl font-semibold">
                      {{ destination.price + " €" }}
                    </p>
                  </div>
                </div>
              </div>
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
  <!-- Footer -->
  <div>
    <p class="bg-orange-50 pb-4 text-center text-xs text-orange-400">© 2024 Toorism. Tous droits réservés.</p>
  </div>
</template>

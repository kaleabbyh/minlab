<template>
  <div
    class="user-profile bg-gray-100 min-h-screen flex items-center justify-center py-20 md:pt-28"
  >
    <div class="bg-white rounded-lg shadow-xl p-8 w-full max-w-xl">
      <div class="relative">
        <div class="flex items-center mb-6">
          <div class="w-24 h-24 rounded-full overflow-hidden">
            <img
              :src="user.avatar"
              :alt="user.name"
              class="w-full h-full object-cover"
            />
          </div>
          <div class="ml-4">
            <h2 class="text-2xl font-semibold text-gray-800">
              {{ user.name }}
            </h2>
            <p class="text-gray-600">{{ user.email }}</p>
            <p class="text-gray-600">{{ user.location }}</p>
            <p class="text-gray-600">
              Joined: {{ formatDate(user.joinedDate) }}
            </p>
          </div>
        </div>
        <router-link
          to="/addrecipe"
          class="bg-green-500 text-white py-1 px-2 rounded absolute right-0 bottom-0 mb-4 mr-4"
        >
          Add Recipe
        </router-link>
      </div>

      <div class="border-t border-gray-300 pt-6">
        <h3 class="text-lg font-semibold mb-4 text-gray-800">About Me</h3>
        <p class="text-gray-600">{{ user.about }}</p>
      </div>

      <div class="border-t border-gray-300 pt-6">
        <h3 class="text-lg font-semibold mb-4 text-gray-800">Recipes</h3>
        <ul class="list-disc list-inside text-gray-600">
          <li
            v-for="recipe in recipes"
            :key="recipe.id"
            class="flex justify-between"
          >
            <span>{{ recipe.title }}</span>
            <div class="flex justify-center">
              <router-link
                :to="`/updaterecipe/${recipe.id}`"
                class="bg-green-500 text-white py-1 px-4 mx-1 my-2 rounded"
              >
                Edit
              </router-link>
              <router-link
                :to="`/recipedetails/${recipe.id}`"
                class="bg-indigo-500 text-white py-1 px-4 mx-1 my-2 rounded"
              >
                view
              </router-link>
            </div>
          </li>
        </ul>
      </div>

      <div class="flex justify-between border-t border-gray-300 pt-6">
        <div class="flex items-end">
          <div>
            <h3 class="text-lg font-semibold mb-4 text-gray-800">Contact</h3>
            <p class="text-gray-600">Email: {{ user.email }}</p>
            <p class="text-gray-600">Phone: {{ user.phone }}</p>
          </div>
        </div>
        <div class="flex items-end">
          <router-link
            :to="`/updateuser/${user.id}`"
            class="bg-indigo-600 text-white py-1 px-2 rounded right-0 bottom-0 mr-4"
          >
            Update Profile
          </router-link>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watchEffect } from "vue";
import PersonImage from "@/assets/images/person.png";
import { useRouter, useRoute } from "vue-router";
import { useQuery } from "@vue/apollo-composable";
import { getUser_Query } from "../api/graphql";

const user = ref({});
const recipes = ref([]);
const route = useRoute();
const router = useRouter();
const userId = route.params.id;

const { result, loading, error } = useQuery(getUser_Query, { id: userId });
const userDetail = computed(() => result.value?.user[0]);

const constants = {
  location: "Addis Ababa",
  about: `Lorem ipsum dolor sit amet, consectetur adipiscing elit. 
  Nullam bibendum purus quis felis aliquet, et tristique ex bibendum.
   Nunc accumsan justo non turpis malesuada, vitae tristique dui venenatis. 
   Donec nec libero magna. Nulla facilisi. Proin tristique, neque id dapibus tincidunt, 
   lacus tortor malesuada ex, id posuere metus sem vitae massa.`,
  phone: "+251922555731",
};

watchEffect(() => {
  if (userDetail.value) {
    try {
      user.value = {
        id: userId,
        name: userDetail.value.firstname + " " + userDetail.value.lastname,
        email: userDetail.value.email,
        joinedDate: userDetail.value.created_at.split("T")[0],
        location: constants.location,
        avatar: PersonImage,
        about: constants.about,
        phone: constants.phone,
      };

      recipes.value = userDetail.value.recipes;
    } catch (error) {
      console.error("Error retrieving user:", error);
    }
  }
});

function formatDate(date) {
  return date;
}
</script>

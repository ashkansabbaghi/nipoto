<script lang="ts" setup>
import type { Post } from "~/types";
const runtimeConfig = useRuntimeConfig();

definePageMeta({
  layout: "blog",
  title: "blogs Nipoto",
  description: "blogs for Nipoto",
});

const posts = ref<Post[]>([]);
const photos = ref<any[]>([]);
const searchTerm = ref<string>("");

//fetch data
const fetchData = async () => {
  try {
    const [postsResponse, photosResponse] = await Promise.all([
      $fetch<Post[]>(`${runtimeConfig.public.apiBase}posts`),
      $fetch<string[]>(`${runtimeConfig.public.apiBase}photos`),
    ]);
    [posts.value, photos.value] = [postsResponse, photosResponse]; // set data
  } catch (error) {
    console.error("Error fetching data:", error);
  }
};
const getImageUrl = (postId: number) => {
  const photo = photos.value.find((photo) => photo.id === postId);
  return photo?.url ?? "";
};

onMounted(() => {
  const savedSearchTerm = localStorage.getItem("searchTerm");
  console.log(savedSearchTerm);
  
  if (savedSearchTerm) {
    searchTerm.value = savedSearchTerm;
  }
  fetchData();
});

const filteredPosts = computed(() => {
  return posts.value.filter(
    (post) =>
      post.title.toLowerCase().includes(searchTerm.value.toLowerCase()) ||
      post.body.toLowerCase().includes(searchTerm.value.toLowerCase())
  );
});
</script>

<template>
  <div class="container mx-auto">
    <div class="mb-4">
      <input
        v-model="searchTerm"
        type="text"
        placeholder="search title | body"
        class="w-full p-2 bg-transparent border-b-2 border-gray-300 rounded focus:ring-0 focus:outline-none focus:border-blue-500 text-start"
      />
    </div>

    <div class="grid grid-cols-1 gap-4 md:grid-cols-2 lg:grid-cols-3">
      <div v-for="post in filteredPosts" :key="post.id" class="p-3 rounded-md shadow-lg card-box">
        <img
          :src="getImageUrl(post.userId)"
          alt="Post Image"
          class="object-cover w-full h-48 rounded-md"
        />
        <h2 :title="post?.title" class="mt-2 text-xl font-semibold truncate">{{ post?.title }}</h2>
        <p class="mt-2 text-gray-600 line-clamp-2">{{ post?.body }}</p>
      </div>
    </div>
  </div>
</template>

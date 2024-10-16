<script lang="ts" setup>
import type { Post } from "~/types";
import { debounce } from "lodash";
const useSearchText = useCookie("searchText");

const runtimeConfig = useRuntimeConfig();

definePageMeta({
  layout: "blog",
  title: "blogs Nipoto",
  description: "blogs for Nipoto",
});

const posts = ref<Post[]>([]);
const photos = ref<any[]>([]);
const searchTerm = ref<string>("");
const delayedSearchTerm = ref<string>("");
const isTyping = ref<boolean>(false);

//fetch data

const { data, status } = await useAsyncData('data', async () => {
  const [postsData, photosData] = await Promise.all([
    $fetch<Post[]>(`${runtimeConfig.public.apiBase}posts`),
    $fetch<any[]>(`${runtimeConfig.public.apiBase}photos`)
  ])

  return { postsData, photosData }
})


posts.value = data.value?.postsData || [];
photos.value = data.value?.photosData || [];

const getImageUrl = (postId: number) => {
  const photo = photos.value.find((photo) => photo.id === postId);
  return photo?.url ?? "";
};

const updateSearchTerm = debounce(() => {
  isTyping.value = false; // set is typing to false
  delayedSearchTerm.value = searchTerm.value;
  // set the search term in cookie
  useSearchText.value = delayedSearchTerm.value;
}, 1000);

watch(searchTerm, () => {
  isTyping.value = true; //  set is typing to true
  updateSearchTerm();
});

onMounted(() => {
  const savedSearchTerm = useSearchText.value;
  savedSearchTerm && (searchTerm.value = savedSearchTerm);
});

const filteredPosts = computed(() => {
  return posts.value.filter(
    (post) =>
      post.title
        .toLowerCase()
        .includes(delayedSearchTerm.value.toLowerCase()) ||
      post.body.toLowerCase().includes(delayedSearchTerm.value.toLowerCase())
  );
});

const highlightText = (text: string) => {
  if (!searchTerm.value) return text;
  const search = new RegExp(`(${searchTerm.value})`, "gi");
  return text.replace(search, '<strong class="text-red-800">$1</strong>');
};
</script>

<template>
  <div class="container mx-auto">
    <div class="inline-flex w-full mb-4">
      <!-- search input -->
      <input
        v-model="searchTerm"
        type="text"
        placeholder="search title | body"
        class="w-full p-2 bg-transparent border-b-2 border-gray-300 rounded focus:ring-0 focus:outline-none focus:border-blue-500 text-start"
      />
      <Spinner class="ml-2" v-if="isTyping" />
    </div>

    <!-- posts -->
    <div class="">
      <!-- pending  -->
      <div v-if="status === 'pending'" class="loading-spinner">
        <SkeletonPost />
      </div>

      <!-- error  -->
      <div v-else-if="status === 'error'" class="error-message">
        خطایی رخ داده است
      </div>

      <!--  post ( render the posts if not pending or error) -->
      <template v-else>
        <div
          v-if="filteredPosts.length > 0"
          class="grid grid-cols-1 gap-4 md:grid-cols-2 lg:grid-cols-3"
        >
          <article
            v-for="post in filteredPosts"
            :key="post.id"
            class="p-3 rounded-md shadow-lg card-box"
          >
            <NuxtImg
              format="webp"
              :src="getImageUrl(post.userId)"
              :alt="post?.title"
              class="object-cover w-full h-48 rounded-md"
            />
            <h2
              :title="post?.title"
              v-html="highlightText(post?.title)"
              class="mt-2 text-xl font-semibold truncate"
            ></h2>
            <p
              :title="post?.body"
              v-html="highlightText(post?.body)"
              class="mt-2 text-gray-600 line-clamp-2"
            ></p>
          </article>
        </div>
        <div v-else class="w-full col-span-3 text-center">
          <p class="w-full">not found article</p>
        </div>
      </template>
    </div>
  </div>
</template>

<script lang="ts" setup></script>

<template>
<div
  class="w-full p-4 text-center bg-white border border-gray-200 rounded-lg shadow sm:p-8  "
>
  <h5 class="mb-2 text-3xl font-bold text-gray-900 ">
    Work fast from anywhere
  </h5>
  <p class="mb-5 text-base text-gray-500 sm:text-lg ">
    Stay up to date and move work forward with Nipoto on iOS & Android. Download the app today.
  </p>
  <div
    class="items-center justify-center space-y-4 sm:flex sm:space-y-0 sm:space-x-4 rtl:space-x-reverse"
  >
    <NuxtLink
      to="/blogs"
      class="w-full sm:w-auto bg-gray-800 hover:bg-gray-700 focus:ring-4 focus:outline-none focus:ring-gray-300 text-white rounded-lg inline-flex items-center justify-center px-4 py-2.5 "
    >
      <div class="text-left rtl:text-right">
        <div class="mb-1 text-xs">Blogs</div>
        <div class="-mt-1 font-sans text-sm font-semibold">Go to Blogs page</div>
      </div>
    </NuxtLink>
  </div>
</div>
</template>

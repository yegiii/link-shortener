<script setup lang="ts">
import { ref } from "vue";
import axios from "axios";

const link = ref("");
const shortenedLink = ref("");
const loading = ref<Boolean>(false);
const token = "142adebca64d8eaf1d4c320a3687562854cd4912";
// const guid = "Ba1bc23dE4F";
// Ba1bc23dE4F

async function validURL() {
  loading.value = true;

  var pattern = new RegExp(
    "^(https?:\\/\\/)?" + // protocol
      "((([a-z\\d]([a-z\\d-]*[a-z\\d])*)\\.)+[a-z]{2,}|" + // domain name
      "((\\d{1,3}\\.){3}\\d{1,3}))" + // OR ip (v4) address
      "(\\:\\d+)?(\\/[-a-z\\d%_.~+]*)*" + // port and path
      "(\\?[;&a-z\\d%_.~+=-]*)?" + // query string
      "(\\#[-a-z\\d_]*)?$",
    "i"
  ); // fragment locator

  const valid = !!pattern.test(link.value);
  if (valid) {
    const url = "https://api-ssl.bitly.com/v4/shorten";
    const data = {
      domain: "bit.ly",
      long_url: link.value,
    };
    const config = {
      headers: {
        Authorization: `Bearer ${token}`,
        "content-Type": "application/json",
      },
    };
    await axios
      .post(url, data, config)
      .then((result) => {
        shortenedLink.value = result.data.link;
        loading.value = false;
      })
      .catch((err) => {
        shortenedLink.value = err.message;
        loading.value = false;
      });
  }
}
</script>

<template>
  <div class="flex justify-around">
    <input
      type="text"
      v-model="link"
      @keydown="validURL()"
      class="border border-blue-600 rounded-md px-4 py-1"
    />
    <button
      @click="validURL()"
      class="text-blue-600 bg-slate-200 border rounded-md px-3"
    >
      Short Your Link
    </button>
  </div>
  <h3 class="text-center my-8 bg-slate-400 rounded-md py-2">
    {{ shortenedLink }}
  </h3>
  <p v-if="loading">Loading ...</p>
</template>

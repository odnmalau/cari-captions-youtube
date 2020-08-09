<template>
  <div class="max-w-lg mx-auto flex p-6 shadow-lg rounded-lg">
    <div class="w-full max-w-lg">
      <div class="flex flex-wrap -mx-3 mb-3">
        <div class="w-full px-3">
          <input
            class="url appearance-none block w-full bg-gray-200 text-gray-700 border border-gray-200 rounded py-3 px-4 mb-3 leading-tight focus:outline-none focus:bg-white focus:border-gray-500"
            v-model="url"
            type="text"
          />
        </div>
      </div>
      <div class="flex flex-wrap -mx-3 mb-2">
        <div class="w-full md:w-1/2 px-3 mb-6 md:mb-0">
          <input
            class="keyword appearance-none block w-full bg-gray-200 text-gray-700 border border-gray-200 rounded py-3 px-4 leading-tight focus:outline-none focus:bg-white focus:border-gray-500"
            v-model="keyword"
            type="text"
            placeholder="Input your keywords"
          />
        </div>
        <div class="w-full md:w-1/2 px-3">
          <button
            class="clear bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded w-full border py-3 px-4 mb-3"
            @click="clear"
          >
            Clear
          </button>
        </div>
      </div>
      <div class="flex flex-wrap -mx-3 mb-3">
        <div class="w-full md:w-1/1 px-3 mb-6 md:mb-0">
          <button
            class="first bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold py-2 px-4 rounded"
            @click="navigasi('first')"
            :disabled="!pagination['first']"
          >
            First
          </button>
          <button
            class="prev bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold py-2 px-4 rounded-l"
            @click="navigasi('prev')"
            :disabled="!pagination['prev']"
          >
            Prev
          </button>
          <button
            class="next bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold py-2 px-4 rounded-r"
            @click="navigasi('next')"
            :disabled="!pagination['next']"
          >
            Next
          </button>
          <button
            class="last bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold py-2 px-4 rounded"
            @click="navigasi('last')"
            :disabled="!pagination['last']"
          >
            Last
          </button>
        </div>
      </div>
      <div class="bg-white py-6 rounded-t flex">
        <div class="flex-grow font-bold text-teal-600">
          Results Total:
        </div>
        <div>
          <span
            class="rounded-lg bg-orange-200 text-orange-600 text-xs px-2 py-1"
          >
            {{ pagination.total }}
          </span>
        </div>
      </div>
      <div class="bg-white py-0 rounded-t flex">
        <div class=" flex-grow font-bold text-teal-600">
          Pages:
        </div>
        <div>
          <span
            class="rounded-lg bg-orange-200 text-orange-600 text-xs px-2 py-1"
          >
            {{ pagination.page }}
          </span>
        </div>
      </div>
      <div class="rounded-b text-sm bg-white">
        <ul class="list-inside">
          <li v-for="(results, i) in listOfResults" :key="i">
            <span v-html="results.text"></span>
            <a
              class="underline text-teal-500"
              :href="`${url}&t=${results.start}s`"
              target="_blank"
              >Youtube</a
            >
            <hr />
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import pDebounce from "p-debounce";

export default {
  data: function() {
    return {
      url: "https://www.youtube.com/watch?v=klnvttPfOUM",
      keyword: "",
      listOfResults: [],
      pagination: {
        first: null,
        last: null,
        prev: null,
        next: null,
        total: 0,
        page: null,
      },
    };
  },
  watch: {
    keyword: pDebounce(async function tanganikeyword(keyword) {
      if (keyword && keyword.length >= 3) {
        await this.search(keyword, this.url);
      } else {
        this.clearResultAndPagination();
      }
    }, 250),
  },
  methods: {
    async search(keyword, url, pagination) {
      try {
        const respon = await fetch(
          pagination
            ? pagination
            : `https://cari-teks-video-api.vercel.app/api/search?q=${keyword}&url=${encodeURIComponent(
                url
              )}`
        ).then((_) => (_.ok ? _.json() : []));

        this.listOfResults = respon.data;
        this.pagination.first = respon.first;
        this.pagination.last = respon.last;
        this.pagination.prev = respon.prev;
        this.pagination.next = respon.next;
        this.pagination.total = respon.total;
        this.pagination.page = respon.page;
      } catch (error) {
        console.log(error);
      }
    },
    async navigasi(type) {
      if (!this.pagination[type]) {
        return;
      }
      await this.search(this.keyword, this.url, this.pagination[type]);
    },
    clear() {
      this.keyword = "";
      this.clearResultAndPagination();
    },
    clearResultAndPagination() {
      this.listOfResults = [];
      this.pagination = {
        first: null,
        last: null,
        prev: null,
        next: null,
        total: 0,
        page: null,
      };
    },
  },
};
</script>

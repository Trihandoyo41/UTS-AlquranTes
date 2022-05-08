<script>
import { RouterView } from 'vue-router'
import { ref } from "vue";
import axios from "axios";
export default {
  data()
  {
    return {
      error: false,
      loading: true,
      juzs: ref([]),
      surahs: ref([])
    }
  },
  components: {
    RouterView
  },
  mounted()
  {
    this.getJuz()
    this.getSurah()
  },
  methods: {
    getJuz()
    {
      axios.get('https://api.quran.com/api/v4/juzs')
        .then(response =>
        {
          this.juzs = response.data.juzs
        })
        .catch(error =>
        {
          console.log(error)
          this.error = true
        })
        .finally(() => this.loading = false)
    },
    getSurah()
    {
      axios.get('https://api.quran.com/api/v4/chapters')
        .then(response =>
        {
          this.surahs = response.data.chapters
        })
        .catch(error =>
        {
          console.log(error)
          this.error = true
        })
        .finally(() => this.loading = false)
    },
  }
}
</script>

<template>
  <section v-if="error">
    <div class="container">
      <h1 class="text-center">KODINGAN ERROR!</h1>
    </div>
  </section>

  <section v-else>
    <div v-if="loading" class="container">
      <h1 class="text-center">Loading...</h1>
    </div>

    <div v-else class="container">
      <ul class="nav nav-pills justify-content-center">
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" data-bs-toggle="dropdown" href="#" role="button"
             aria-expanded="false">Juz</a>
          <ul class="dropdown-menu">
            <li v-for="juz in juzs" :key="juz.id">
              <router-link :to="{ name: 'juzs', params: { id: juz.id } }" class="dropdown-item">{{ juz.juz_number }}
              </router-link>
            </li>
          </ul>
        </li>
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" data-bs-toggle="dropdown" href="#" role="button"
             aria-expanded="false">Surah</a>
          <ul class="dropdown-menu">
            <li v-for="surah in surahs" :key="surah.id">
              <router-link :to="{ name: 'surahs', params: { id: surah.id } }" class="dropdown-item">{{
                  surah.name_complex
                }}</router-link>
            </li>
          </ul>
        </li>
        <li class="nav-item">
          <router-link :to="{ name: 'search' }" class="nav-link">Search</router-link>
        </li>
        <li class="nav-item">
          <router-link :to="{ name: 'random' }" class="nav-link">Random</router-link>
        </li>

      </ul>
      <RouterView />
    </div>
  </section>
</template>
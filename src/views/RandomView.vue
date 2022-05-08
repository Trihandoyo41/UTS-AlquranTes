<script>
export default {
  data() {
    return {
      isLoading: false,
      verse: null,
      quran: null,
      chapter: null,
      translation: null,
      tafsir: "",
      audio: null,
    };
  },
  methods: {
    async getRandomAyat() {
      const random = Math.floor(Math.random() * 100);
      this.fetchQuran("verses/random?language=id&words=false&translations=id&audio=3&fields=" + random)
        .then(res => {
          const verse = res.verse;
          this.verse = verse;
          this.audio = this.getAudioPath(verse.audio.url);
          /**
           * Get verse key
           */
          const verseKey = verse.verse_key;
          this.getQuran(verseKey);
          /**
           * Get chapter number
           */
          const chapterNumber = verseKey.split(':')[0];
          this.getChapter(chapterNumber);
          /**
           * Get translate
           */
          this.getTranslate(verseKey);
        })
    },
    async getQuran(verseKey) {
      this.fetchQuran('quran/verses/uthmani?verse_key=' + verseKey)
        .then(res => {
          this.quran = res.verses[0]
        })
    },
    getAudioPath(path) {
      return "https://verses.quran.com/" + path;
    },
    async getChapter(id) {
      this.fetchQuran("chapters/" + id + "?language=id").then((res) => {
        this.chapter = res.chapter;
      });
    },
    async getTranslate(verseKey) {
      this.fetchQuran("quran/translations/33?verse_key=" + verseKey).then(
        (res) => {
          this.translation = res.translations[0];
        }
      );
    },
    async getTafsir() {
    },
    async fetchQuran(path) {
      this.isLoading = true;
      const url = "https://api.quran.com/api/v4/" + path;
      return fetch(url, {
        method: "GET",
        headers: {
          "Content-Type": "application/json",
        },
      })
        .then((res) => {
          if (!res.ok) {
            const error = new Error(res.statusText);
            error.json = res.json();
            throw error;
          }
          return res.json();
        })
        .catch((err) => {
          alert(err.toString());
        })
        .finally(() => {
          this.isLoading = false;
        });
    },
  },
  mounted() {
    this.getRandomAyat();
    this.getQuran();
    this.getTafsir();
    this.getAudioPath();
    this.getTranslate();
  },
};
</script>

<template>
  <section className="hero is-small">
    <div className="hero-body">
      <p className="title">Random Ayat</p>
      <p className="subtitle">Random Ayat from https://api.quran.com/</p>
    </div>
  </section>
  <section className="content">
    <div>
      <h2 v-if="chapter" className="has-text-right">
        {{ chapter.name_arabic }} {{ verse.verse_number }}
      </h2>
      <p v-if="audio" className="has-text-right">
        <audio controls>
          <source :src="audio" type="audio/mpeg" />
          Your browser does not support the audio element.
        </audio>
      </p>
      <h3 v-if="quran" className="has-text-right quran">
        {{ quran.text_uthmani }}
      </h3>
      <p v-if="translation">{{ translation.text }}</p>
    </div>
    <div className="has-text-centered" v-if="isLoading">
      <i className="fa-solid fa-spinner fa-pulse"></i>
    </div>
  </section>
</template>
<template>
  <section class="container">
    <div v-if="loading">
      <div
        v-for="i in 5"
        :key="i"
        class="skeleton skeleton_row skeleton_row--big" />
    </div>
    <div
      v-else>
      <div class="search clearfix">
        <label
          for="search-surah"
          class="search__title">
          Pencarian surat
        </label>
        <input
          id="search-surah"
          v-model="searchText"
          type="search"
          name="search"
          placeholder="Surat apa yang ingin dibaca hari ini?">
      </div>

      <div class="all-surah">
        <SurahCard :surah-array="filteredSurah" />
      </div>
    </div>
  </section>
</template>

<script>
import { mapActions, mapMutations, mapState } from 'vuex'

import SurahCard from '../components/SurahCard.vue'
import { __isNotEmptyString, __normalizeText } from '../utils/index'

export default {
  name: 'PageAllSurah',
  head () {
    return {
      title: 'Daftar Surat | Qur\'an Offline'
    }
  },
  components: {
    SurahCard
  },
  data () {
    return {
      loading: true,
      searchText: ''
    }
  },
  computed: {
    ...mapState([
      'allSurahList'
    ]),
    isHaveSource () {
      return __isNotEmptyString(this.$route.query.source)
    },
    filteredSurah () {
      if (__isNotEmptyString(this.searchText) && this.searchText.length >= 3) {
        return this.allSurahList.filter(item => {
          let predicateTranslation = __normalizeText(item.translation).includes(
            __normalizeText(this.searchText)
          )
          let predicateLatin = __normalizeText(item.latin).includes(
            __normalizeText(this.searchText)
          )

          return predicateLatin || predicateTranslation
        })
      } else return this.allSurahList
    }
  },
  mounted () {
    this.setHeaderTitle('Daftar Surat')
    this.fetchSurahInfo()
  },
  methods: {
    ...mapMutations([
      'setHeaderTitle'
    ]),
    ...mapActions([
      'fetchAllSurah'
    ]),
    fetchSurahInfo () {
      this.fetchAllSurah({
        success: () => {
          if (!this.isHaveSource) {
            setTimeout(() => {
              this.loading = false
            }, 1000)
          } else this.loading = false
        }
      })
    }
  }
}
</script>

<style lang="scss" scoped>
@import '@/assets/search.scss';
.all-surah{
  width: 90%;
  margin: 0 auto;
}
</style>

<template>
  <div class="home">
    <AppHeader />

    <div class="search-row">
      <input v-model="search" class="search" placeholder="Search by name…" />
      <p v-if="!loading && search_pokemons.length > 0" class="meta">
        Results: {{ search_pokemons.length }}
      </p>
    </div>

    <div v-if="loading" class="spinner-wrap">
      <div class="spinner"></div>
    </div>

    <div v-else-if="search_pokemons.length === 0" class="no-result">
      No Pokémon found matching your search.
    </div>

    <div v-else class="grid">
      <pokemon-card
        v-for="pokemon in search_pokemons"
        :key="pokemon.id"
        class="card"
        :pokemon="pokemon"
        @select-pokemon="openModal"
      />
    </div>

    <!-- ===== Modal ===== -->
    <transition name="fade">
      <div
        v-if="isModalOpen"
        class="modal-overlay"
        @click.self="closeModal"
      >
        <transition name="pop" appear>
          <app-modal
            :selected="selected"
            :details="details"
            @closeModal="closeModal"
          />
        </transition>
      </div>
    </transition>


    <div class="actions" v-if="!loading && search_pokemons.length > 0">
      <button class="btn" @click="loadMore" :disabled="loadingMore">
        <span v-if="loadingMore">Loading…</span>
        <span v-else>Load more</span>
      </button>
    </div>
  </div>
</template>

<script>
import AppHeader from '@/components/AppHeader.vue';
import AppModal from '@/components/AppModal.vue';
import PokemonCard from '@/components/PokemonCard.vue';
export default {
  components: {
    AppHeader,
    PokemonCard,
    AppModal
  },
  name: 'App',
  data() {
    return {
      search: '',
      pokemons: [],
      offset: 0,
      limit: 48,
      loading: true,
      loadingMore: false,
      selected: null,
      isModalOpen: false,
      details: null,
    }
  },
  computed: {
    search_pokemons() {
      let searchWord = this.search.trim()
      if (searchWord === '') {
        return this.pokemons
      }
      return this.pokemons.filter(p =>
        p.name.toLowerCase().includes(searchWord.toLowerCase())
      )
    }
  },
  methods: {
    async pokemonApi(offset, limit) {
      try {
        // ここは fetch 専用（データだけ取得）
        const listUrl = `https://pokeapi.co/api/v2/pokemon?offset=${offset}&limit=${limit}`
        const listRes = await fetch(listUrl)
        
        if (listRes.status !== 200) {
          throw new Error(listRes.status);
        }
        // 一度APIを叩いて、各ポケモンの詳細URL（エンドポイント一覧）を取得
        const listData = await listRes.json()

        // 各ポケモンの詳細データを更に叩く
        const details = await Promise.all(
          listData.results.map(async (item) => {
            const res = await fetch(item.url)

            if (res.status !== 200) {
              throw new Error(res.status);
            }

            const detail = await res.json()

            const stats = Object.fromEntries(
              detail.stats.map(s => [s.stat.name, s.base_stat])
            )
            const types = detail.types.map(t => t.type.name)

            // 取得したAPIレスポンスを、テンプレートで扱いやすいように整形
            return {
              id: detail.id,
              name: detail.name.charAt(0).toUpperCase() + detail.name.slice(1),
              image:
                detail.sprites?.other?.['official-artwork']?.front_default ??
                detail.sprites?.front_default ??
                '',
              types,
              hp:  stats.hp ?? 50,
              atk: stats.attack ?? 50,
              def: stats.defense ?? 50,
              spd: stats.speed ?? 50,
              spAtk: stats['special-attack'] ?? 50,
              spDef: stats['special-defense'] ?? 50,
              _raw: detail,
            }
          })
        )
        return details
      } catch (error) {
        console.log(error);
      }
      this.loading = false
    },
    async loadInitial() {
      this.loading = true
      try {
        const pokemonDatas = await this.pokemonApi(this.offset, this.limit)
        this.pokemons = pokemonDatas
      } catch (error) {
        console.log(error);
      } 
      this.loading = false
    },
    async loadMore() {
      if(this.loading || this.loadingMore) {
        return
      }
      this.loadingMore = true
      try {
        this.offset = this.offset + this.limit
        const morePokemons = await this.pokemonApi(this.offset, this.limit)
         this.pokemons = [...this.pokemons, ...morePokemons]
      } catch (error) {
        console.log(error);
      } 
      this.loadingMore = false
    },
    async openModal(p) {
      this.selected = p
      this.details = p._raw 
      this.isModalOpen = true
    },
    closeModal() {
      this.isModalOpen = false
      this.selected = null
      this.details = null
      document.body.style.overflow = ''
    },
  },
  mounted() {
    this.loadInitial()
  }
}
</script>

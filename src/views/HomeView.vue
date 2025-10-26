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
      >
      </pokemon-card>
    </div>

    <!-- ===== Modal ===== -->
    <transition name="fade">
      <div
        v-if="isModalOpen"
        class="modal-overlay"
        @click.self="closeModal"
        role="dialog"
        :aria-labelledby="'modal-title'"
        aria-modal="true"
      >
        <!-- モーダル本体にもトランジション -->
        <transition name="pop" appear>
          <div class="modal" ref="modalRef">
            <button class="modal-close" @click="closeModal" aria-label="Close dialog">×</button>
          
            <div class="modal-header">
              <h2 id="modal-title">{{ selected?.name }}</h2>
              <div class="tags">
                <span class="type" v-for="t in selected?.types || []" :key="t" :data-type="t">{{ t }}</span>
                <span class="id">#{{ String(selected?.id || 0).padStart(3, '0') }}</span>
              </div>
            </div>
          
            <div class="modal-body">
              <div class="modal-hero">
                <img :src="selected?.image" :alt="selected?.displayName" />
              </div>
            
              <div class="modal-info">
                <ul class="kv">
                  <li><b>HP</b><span>{{ selected?.hp }}</span></li>
                  <li><b>ATK</b><span>{{ selected?.atk }}</span></li>
                  <li><b>DEF</b><span>{{ selected?.def }}</span></li>
                  <li><b>SPD</b><span>{{ selected?.spd }}</span></li>
                  <li><b>Height</b><span>{{ details?.height }} dm</span></li>
                  <li><b>Weight</b><span>{{ details?.weight }} hg</span></li>
                </ul>
              
                <div class="abilities" v-if="(details?.abilities || []).length">
                  <h3>Abilities</h3>
                  <ul>
                    <li v-for="ab in details.abilities" :key="ab.ability.name">
                      {{ ab.ability.name }} <small v-if="ab.is_hidden">(hidden)</small>
                    </li>
                  </ul>
                </div>
              
                <div class="moves" v-if="(details?.moves || []).length">
                  <h3>Moves</h3>
                  <ul>
                    <li v-for="mv in details.moves.slice(0, 6)" :key="mv.move.name">{{ mv.move.name }}</li>
                  </ul>
                  <small>…and {{ Math.max(0, details.moves.length - 6) }} more</small>
                </div>
              </div>
            </div>
          
            <div class="modal-actions">
              <button class="btn" @click="closeModal">Close</button>
            </div>
          </div>
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
import PokemonCard from '@/components/PokemonCard.vue';
export default {
  components: {
    AppHeader,
    PokemonCard
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
          throw new Error(`List HTTP ${listRes.status}`);
        }
        // 一度APIを叩いて、各ポケモンの詳細URL（エンドポイント一覧）を取得
        const listData = await listRes.json()

        // 各ポケモンの詳細データを更に叩く
        const details = await Promise.all(
          listData.results.map(async (item) => {
            const res = await fetch(item.url)

            if (res.status !== 200) {
              throw new Error(`List HTTP ${listRes.status}`);
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

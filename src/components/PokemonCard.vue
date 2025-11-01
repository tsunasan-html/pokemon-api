<template>
  <article 
    class="card"
    :data-type="pokemon.types[0] || 'normal'"
    @click="openModal(pokemon)"
    >
    <div class="card-frame">
      <!-- 名前・HP -->
      <div class="card-header">
        <span class="name">{{ pokemon.name }}</span>
        <span class="hp">HP {{ pokemon.hp }}</span>
      </div>

      <div class="image-wrap">
        <img :src="pokemon.image || require('@/assets/no-image.svg')" :alt="pokemon.name" loading="lazy" />
      </div>

      <div class="types">
        <span v-for="t in pokemon.types" :key="t" class="type" :data-type="t">
          {{ t }}
        </span>
        <span class="id">#{{ String(pokemon.id).padStart(3, '0') }}</span>
      </div>

      <div class="stats">
        <div class="stat">
          <label>ATK</label>
          <div class="bar">
            <span :style="{ width: Math.min(pokemon.atk, 100) + '%' }"></span>
          </div>
        </div>
        <div class="stat">
          <label>DEF</label>
          <div class="bar">
             <span :style="{ width: Math.min(pokemon.def, 100) + '%' }"></span>
          </div>
        </div>
        <div class="stat">
          <label>SPD</label>
          <div class="bar">
            <span :style="{ width: Math.min(pokemon.spd, 100) + '%' }"></span>
          </div>
        </div>
      </div>
    </div>
  </article>
</template>

<script>
export default {
  props: {
    pokemon: Object,
  },
  methods: {
    async openModal(p) {
      this.$emit("select-pokemon", p)
    }
  }
}
</script>

<style>

</style>
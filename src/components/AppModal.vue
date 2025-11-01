<template>
  <div class="modal">
    <button class="modal-close" @click="closeModal">×</button>
  
    <div class="modal-header">
      <h2>{{ selected?.name }}</h2>
      <div class="tags">
        <span class="type" v-for="t in selected?.types || []" :key="t" :data-type="t">{{ t }}</span>
        <span class="id">#{{ String(selected?.id || 0).padStart(3, '0') }}</span>
      </div>
    </div>
  
    <div class="modal-body">
      <div class="modal-hero">
        <img :src="selected?.image" :alt="selected?.name" />
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
</template>

<script>
export default {
  props: {
    selected: Object,
    details: Object,
  },
  methods: {
    closeModal() {
      this.$emit("closeModal")
    },
  }
}
</script>

<style>

</style>
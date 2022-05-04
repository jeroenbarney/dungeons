<script lang="ts">
import {defineComponent} from "vue";

export default defineComponent({
  data: () => ({
    dungeons: [
      {short: 'tazs', name: `Tazavesh: Streets of Wonder`},
      {short: 'tazg', name: `Tazavesh: So'leah's Gambit`},
      {short: 'hoa', name: `Halls of Atonement`},
      {short: 'sd', name: `Sanguine Depths`},
      {short: 'soa', name: `Spires of Ascension`},
      {short: 'pf', name: `Plaguefall`},
      {short: 'dos', name: `De Other Side`},
      {short: 'mots', name: `Mists of Tirna Scithe`},
      {short: 'nw', name: `The Necrotic Wake`},
      {short: 'top', name: `Theater of Pain`},
    ],
    selected: [] as string[],
  }),
  mounted() {
    try {
      this.selected = JSON.parse(localStorage.getItem('selected_dungeons') ?? '');
    } catch (e) {
      this.selected = [];
    }
  },
  watch: {
    selected: {
      deep: true,
      handler() {
        localStorage.setItem('selected_dungeons', JSON.stringify(this.selected));
      }
    }
  },
  computed: {
    all() {
      return this.selected.length === this.dungeons.length;
    },
    selectedText() {
      if (this.selected.length === 0) return '';

      return ` and (${this.selected.join(' or ')})`;
    }
  },
  methods: {
    copy() {
      navigator.clipboard.writeText(`partyfit${this.selectedText}`);
    },
    click(dungeon: { short: string }) {
      const index = this.selected.indexOf(dungeon.short);
      if (index > -1) {
        this.selected.splice(index, 1);
      } else {
        this.selected.push(dungeon.short);
      }
    },
    toggleAll() {
      this.selected = this.all ? [] : this.dungeons.map(item => item.short);
    }
  }
});
</script>

<style>
.dungeons {
  display: grid;
  grid-template-columns: repeat(3, auto);
  place-content: center;
  user-select: none;
}

.dungeon {
  display: contents;
  cursor: pointer;
}

.dungeon > * {
  padding: 0 10px;
}

pre {
  user-select: all;
}

body {
  display: grid;
  place-items: center;
  height: 100vh;
}

.line {
  grid-column: 1/-1;
  height: 1px;
  width: 100%;
  background: #d3d3d3;
  margin: 3px;
}
</style>

<template>
  <div class="dungeons">
    <div class="dungeon" @click="toggleAll()">
      <input type="checkbox" :checked="all">
      <span style="font-weight: bold;text-decoration: underline; text-align: center;">Allemaal</span>
      <span></span>
      <div class="line"></div>
    </div>
    <div class="dungeon" v-for="dungeon in dungeons" @click="click(dungeon)">
      <input v-model="selected" :value="dungeon.short" type="checkbox">
      <span>{{ dungeon.name }}</span>
      <span>{{ dungeon.short }}</span>
      <div class="line"></div>
    </div>
  </div>
  <pre @click="copy()">partyfit{{ selectedText }}</pre>
</template>
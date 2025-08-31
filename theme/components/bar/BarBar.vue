<script setup>
import { useRoute, useData } from "vitepress";
import { drawingEnabled } from '../../composables/draw'
import { ref } from "vue";
import { tempo } from "#/use/tempo";
import { globalScale, noteColor, notes } from "#/use";
import { mic } from "#/use/mic";
import { midi } from "#/use/midi"

import FullScreen from '../global/FullScreen.vue'
import BarPanel from "./BarPanel.vue";

const route = useRoute()
const { theme } = useData()

const aboutOpen = ref()
const administrationOpen = ref()
const ministryOpen = ref()
const serveOpen = ref()

const searchOpen = ref()
const connectOpen = ref()
const joinOpen = ref()
const causesOpen = ref()
const villageOpen = ref()

</script>

<template lang='pug'>
nav.bar
  a(
    href="/"
    v-tooltip.right="'Nation of God and Christ'"
    aria-label="Back to main page"
    )
    img.cursor-pointer.mt-3.mb-2.w-10(src="https://raw.githubusercontent.com/nogac-org/nogac.org/main/content/public/media/logo/logo.png", alt="NOGAC logo" title="NOGAC")

  button(
    :inert="aboutOpen"
    @click="aboutOpen = !aboutOpen"
    :class="{ active: aboutOpen || route.path.includes('a.about') }"
    title="About"
    aria-label="Toggle about navigation panel"
    v-tooltip.right="'About'"
    )
    .i-la-users
  button(
    :inert="administrationOpen"
    @click="administrationOpen = !administrationOpen"
    :class="{ active: administrationOpen || route.path.includes('b.administration') }"
    title="Administration"
    aria-label="Toggle administration navigation panel"
    v-tooltip.right="'Administration'"
    )
    .i-la-church
  button(
    :inert="ministryOpen"
    @click="ministryOpen = !ministryOpen"
    :class="{ active: ministryOpen || route.path.includes('c.ministries') }"
    title="Ministries"
    aria-label="Toggle ministries navigation panel"
    v-tooltip.right="'Ministries'"
    )
    .i-la-seedling
  button(
    :inert="serveOpen"
    @click="serveOpen = !serveOpen"
    :class="{ active: serveOpen || route.path.includes('d.serve') }"
    title="How We Serve"
    aria-label="Toggle serve navigation panel"
    v-tooltip.right="'How We Serve'"
    )
    .i-la-hand-holding-heart
  
  .spacer

  button(
    title="Search"
    :inert="searchOpen"
    @click="searchOpen = !searchOpen"
    :class="{ active: searchOpen, 'touch-action-none': searchOpen }"
    v-tooltip.right="'Search'"
    aria-label="Toggle search panel"
    )
    .i-la-search

  .spacer

  button(
    :inert="joinOpen"
    @click="joinOpen = !joinOpen"
    :class="{ active: joinOpen || route.path.includes('e.join') }"
    title="Serve With Us"
    aria-label="Toggle join navigation panel"
    v-tooltip.right="'Serve With Us'"
    )
    .i-la-door-open

  button(
    :inert="villageOpen"
    @click="villageOpen = !villageOpen"
    :class="{ active: villageOpen || route.path.includes('f.villages') }"
    title="It Takes a Village"
    aria-label="Toggle village navigation panel"
    v-tooltip.right="'It Takes a Village'"
    )
    .i-la-people-carry

  button(
    :inert="causesOpen"
    @click="causesOpen = !causesOpen"
    :class="{ active: causesOpen || route.path.includes('g.causes') }"
    title="Our Initiatives"
    aria-label="Toggle initiatives navigation panel"
    v-tooltip.right="'Our Initiatives'"
    )
    .i-la-leaf

  button(
    :inert="connectOpen"
    @click="connectOpen = !connectOpen"
    :class="{ active: connectOpen || route.path.includes('h.connect') }"
    title="Connect"
    aria-label="Toggle connect navigation panel"
    v-tooltip.right="'Connect'"
    )
    .i-la-hands-helping

  .flex-auto
  .spacer 

  
  state-dark
  full-screen


client-only

  BarPanel(v-model="searchOpen")
    nav-search.m-4(@close="searchOpen = false" :focus="searchOpen")

  BarPanel(v-model="aboutOpen")
    a.text-2xl.p-2.m-2.block.font-bold.flex.gap-2.items-center(
      href="/a.about/"
      ) 
      .i-la-users
      .p-0 About
    BarLevel(path="/a.about/" :level="0")

  BarPanel(v-model="administrationOpen")
    a.text-2xl.p-2.m-2.block.font-bold.flex.gap-2.items-center(
      href="/b.administration/"
      ) 
      .i-la-church
      .p-0 Administration
    BarLevel(path="/b.administration/" :level="0" @close="administrationOpen = false")

  BarPanel(v-model="ministryOpen")
    a.text-2xl.p-2.m-2.block.font-bold.flex.gap-2.items-center(
      href="/c.ministries/"
      ) 
      .i-la-hand-holding-heart
      .p-0 Ministries
    BarLevel(path="/c.ministries/" :level="0" @close="ministryOpen = false")

  BarPanel(v-model="serveOpen")
    a.text-2xl.p-2.m-2.block.font-bold.flex.gap-2.items-center(
      href="/d.serve/"
      ) 
      .i-la-hand-holding-heart
      .p-0 How We Serve
    BarLevel(path="/d.serve/" :level="0" @close="serveOpen = false")

  BarPanel(v-model="joinOpen")
    a.text-2xl.p-2.m-2.block.font-bold.flex.gap-2.items-center(
      href="/e.join/"
      ) 
      .i-la-door-open
      .p-0 Serve wtih Us
    BarLevel(path="/e.join/" :level="0" @close="joinOpen = false")

  BarPanel(v-model="villageOpen")
    a.text-2xl.p-2.m-2.block.font-bold.flex.gap-2.items-center(
      href="/f.villages/"
      ) 
      .i-la-leaf
      .p-0 It Takes a Village
    BarLevel(path="/f.villages/" :level="0" @close="villageOpen = false")

  BarPanel(v-model="causesOpen")
    a.text-2xl.p-2.m-2.block.font-bold.flex.gap-2.items-center(
      href="/g.causes/"
      ) 
      .i-la-leaf
      .p-0 Our Initiatives
    BarLevel(path="/g.causes/" :level="0" @close="causesOpen = false")

  BarPanel(v-model="connectOpen")
    a.text-2xl.p-2.m-2.block.font-bold.flex.gap-2.items-center(
      href="/h.connect/"
      ) 
      .i-la-hands-helping
      .p-0 Connect
    BarLevel(path="/h.connect/" :level="0" @close="connectOpen = false")  
</template>

<style lang="postcss" scoped>
nav.bar {
  @apply bg-light-800 dark-bg-dark-400 fixed z-1000 top-0 bottom-0 w-12 shadow flex flex-col items-center max-h-100dvh pb-2 overflow-scroll;
  scrollbar-width: none;
}

nav button,
nav a.button {
  @apply transition text-2xl p-2 flex flex-col items-center opacity-70 hover-opacity-100 grayscale-30;


}

button.active,
.button.active {
  @apply opacity-100 grayscale-0
}


.text-button.active {
  @apply bg-light-200 bg-dark-200 opacity-100 grayscale-0
}

.panel {
  @apply flex flex-col gap-4 transition fixed bg-light-200 z-900 top-0 bottom-0 left-0 shadow-lg ml-12 overflow-scroll p-2 dark-bg-dark-200 max-w-340px pb-8;

  & h2 {
    @apply my-4 text-2xl
  }

}

.spacer {
  @apply w-full bg-dark-200/40 dark-bg-light-100/30 p-0.5px my-1
}
</style>
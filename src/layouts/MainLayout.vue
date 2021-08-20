<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="toggleLeftDrawer"
        />

        <q-toolbar-title>Mobbeel - Technical challenge</q-toolbar-title>

        <div>Quasar v{{ $q.version }}</div>
      </q-toolbar>
    </q-header>

    <q-drawer v-model="leftDrawerOpen" show-if-above bordered class="bg-grey-1">
      <q-list>
        <q-item-label header class="text-primary">
          Essential Links
        </q-item-label>

        <EssentialLink
          v-for="link in essentialLinks"
          :key="link.title"
          v-bind="link"
        />
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import EssentialLink from "components/EssentialLink.vue";
import { mdiEmoticonTongue, mdiVuejs } from "@quasar/extras/mdi-v5";

const linksList = [
  {
    title: "Manuel Muñoz Gil",
    caption: "manuelmg.com",
    icon: mdiEmoticonTongue,
    link: "https://www.manuelmg.com/",
  },
  {
    title: "Code",
    caption: "github.com/mobbeel-technical-test",
    icon: "code",
    link: "https://github.com/MAMUGI1492/mobbeel-technical-test",
  },
  {
    title: "Vue.js v3",
    caption: "v3.vuejs.org",
    icon: mdiVuejs,
    link: "https://v3.vuejs.org/",
  },
  {
    title: "Quasar Framework v2",
    caption: "quasar.dev",
    icon: "favorite",
    link: "https://quasar.dev",
  },
];

import { defineComponent, ref } from "vue";

export default defineComponent({
  name: "MainLayout",

  components: {
    EssentialLink,
  },

  setup() {
    const leftDrawerOpen = ref(false);

    return {
      essentialLinks: linksList,
      leftDrawerOpen,
      toggleLeftDrawer() {
        leftDrawerOpen.value = !leftDrawerOpen.value;
      },
    };
  },
});
</script>

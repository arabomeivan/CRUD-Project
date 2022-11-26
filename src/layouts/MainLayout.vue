<template>
  <q-layout view="lHh Lpr lFf">
    <q-page-container>
      <q-toolbar class="bg-primary text-white">
        <q-avatar>
          <img src="https://cdn.quasar.dev/logo-v2/svg/logo-mono-white.svg" />
        </q-avatar>

        <q-toolbar-title>Ivan's CRUD Application</q-toolbar-title>

        <q-btn flat round dense icon="whatshot" />
      </q-toolbar>

      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent, ref } from "vue";
import EssentialLink from "components/EssentialLink.vue";

export default defineComponent({
  name: "MainLayout",
});
</script>

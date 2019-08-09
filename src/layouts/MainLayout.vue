<template>
  <q-layout view="hHh Lpr fFf">
    <!-- Be sure to play with the Layout demo on docs -->
    <!-- (Optional) A Drawer; you can add one more with side="right" or change this one's side -->
    <q-drawer v-model="leftDrawer" side="left" bordered content-class="bg-grey-2">
      <!-- QScrollArea is optional -->
      <q-scroll-area class="fit q-pa-sm">
        <!-- Content here -->
        <q-list>
          <q-item-label header>Calculadora de Juros</q-item-label>
          <q-item clickable href="#/">
            <q-item-section>Calculadora</q-item-section>
          </q-item>
        </q-list>
      </q-scroll-area>
    </q-drawer>

    <q-page-container>
      <!-- This is where pages get injected -->
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { QDrawer, QItem, QItemLabel, QList, QScrollArea } from 'quasar'
export default {
  name: 'MainLayout',
  components: { QDrawer, QItem, QItemLabel, QList, QScrollArea },
  data() {
    return {
      leftDrawer: true
    }
  }
}
</script>

<template>
  <q-page padding>
    <parcelamento class="q-ma-sm" tipo="composto 1"/>
    <parcelamento class="q-ma-sm" tipo="composto 2"/>
  </q-page>
</template>

<script>
import Parcelamento from 'components/Parcelamento'

export default {
  name: 'CalculadoraPage',
  components: { Parcelamento }
}
</script>

<style>
</style>

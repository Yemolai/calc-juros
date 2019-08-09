<template>
  <q-card class="my-card bg-primary text-white q-pa-md">
    <q-card-section>
      <div class="text-h6">Parcelamento {{tipo}}</div>
      <div class="text-subtitle2"></div>
    </q-card-section>
    <q-card-section>
      <q-form>
        <div class="row">
          <div class="col-xs-12 col-sm-4 q-pr-sm">
            <q-input
              dark
              label="Valor"
              prefix="$"
              hint="use ponto como separador decimal"
              v-model="value"
              :rules="[shouldBePositive]"
              type="number"
              :min="0"
            />
          </div>
          <div class="col-xs-12 col-sm-4 q-pr-sm">
            <q-input
              dark
              label="Juros inicial"
              hint="aplicado apenas na primeira parcela"
              suffix="%"
              v-model="initial"
              :rules="[shouldBePositive]"
              type="number"
              :min="0"
            />
          </div>
          <div class="col-xs-12 col-sm-4 q-pr-sm">
            <q-input
              dark
              label="Por Parcela"
              hint="aplicado apenas a partir da segunda parcela"
              suffix="%"
              v-model="added"
              :rules="[shouldBePositive]"
              type="number"
              :min="0"
            />
          </div>
        </div>
        <div class="row">
          <div class="col-xs-12 col-sm-3">
            <q-select dark label="Parcelas" v-model="parcels" :options="parcelOptions" :disable="showAllParcelas" />
          </div>
          <div class="col-xs-6 col-sm-2 q-pt-md">
            <q-checkbox dark label="Mostrar Min/Max" :disable="showAllParcelas" v-model="showMinMax"/>
          </div>
          <div class="col-xs-6 col-2 q-pt-md">
            <q-checkbox dark label="Mostrar todas" v-model="showAllParcelas"/>
          </div>
        </div>
      </q-form>
      <q-separator spaced dark/>
      <q-list class="q-px-0 q-mx-0" dense dark separator>
        <q-item header>Previsão</q-item>
        <q-item v-for="(parcela, k) in parcelas" :key="'parcela-'+k">
          <q-item-section avatar>{{parcela.p}}x</q-item-section>
          <q-item-section>
            Parcela de {{ toMoney(parcela.parcel) }} <br/>
            Total {{ toMoney(parcela.value) }}
          </q-item-section>
          <q-item-section side>+{{ toMoney(parcela.diff) }}</q-item-section>
        </q-item>
      </q-list>
    </q-card-section>
  </q-card>
</template>

<script>
import {
  QCard,
  QCardSection,
  QSelect,
  QList,
  QForm,
  QItem,
  QItemSection,
  QInput,
  QCheckbox,
  QSeparator
} from 'quasar'

export default {
  name: 'ParcelamentoComponent',
  props: {
    tipo: String
  },
  components: {
    QCard,
    QCardSection,
    QSelect,
    QList,
    QForm,
    QItem,
    QItemSection,
    QInput,
    QCheckbox,
    QSeparator
  },
  data: () => ({
    showAllParcelas: false,
    showMinMax: false,
    parcels: { label: '3x', value: 3 },
    types: ['amigo', 'boleto'],
    initial: 2.98,
    value: 1200,
    added: 3.5
  }),
  computed: {
    parcelOptions() {
      return new Array(12)
        .fill(0)
        .map((_, idx) => ({ label: `${idx + 1}x`, value: idx + 1 }))
    },
    parcelas() {
      const { showMinMax: minmax } = this
      const max = 12
      const parcela = this.parcels.value
      const parcelas = new Array(max).fill(1).map((_, idx) => idx)
      const parcelasFiltradas = this.showAllParcelas
        ? parcelas
        : parcelas.filter((val, i, arr) => (minmax && (i < 1 || i === max - 1)) || i === parcela - 1)
      return parcelasFiltradas.map(idx => this.calcular(idx + 1))
    }
  },
  methods: {
    shouldBePositive: v => Number(v) >= 0 || 'Valor deve ser positivo',
    calcular(p) {
      if (p < 0) {
        return 0
      }
      const parcelas = Math.floor(p)
      const { value } = this
      const inicial = 1 + this.initial / 100
      const composto = 1 + (this.added * parcelas) / 100
      const parcela = value / parcelas
      const primeira = parcela * inicial
      const val =
        new Array(parcelas - 1).fill(parcela).reduce((prev, curr, idx) => {
          return prev + curr * composto
        }, 0) + primeira
      return { p, value: val, parcel: val / parcelas, diff: val - value }
    },
    toMoney(val, locale = 'pt-BR', currency = 'BRL') {
      return Number(val).toLocaleString('pt-BR', {
        style: 'currency',
        currency: 'BRL'
      })
    }
  }
}
</script>

<style>
.my-card {
  min-width: 300px;
  max-width: 800px;
}
.middle {
  align-self: center;
}
</style>

<template>
  <form class="form-group" @submit.prevent="submit">
    <div>
      <label>Elige el producto en promoción</label>
      <multi-select
        v-model="promotion.product"
        :options="products"
        track-by="id"
        label="name"
        placeholder="Productos"
        select-label=""
        deselect-label=""
        class="select"
      />
      <div v-if="$v.promotion.product.$dirty">
        <span class="error" v-if="!$v.promotion.product.required">Este campo es obligatorio.</span>
      </div>
    </div>
    <div>
      <label>Descuento a aplicar en el producto</label>
      <input placeholder="Ejemplo: 1.55" type="text" v-model.number="promotion.discount" />
      <div v-if="$v.promotion.discount.$dirty">
        <span class="error" v-if="!$v.promotion.discount.required">Este campo es obligatorio.</span>
        <span class="error" v-else-if="!$v.promotion.discount.between">Valores validos entre 0.05 y 99.95</span>
        <span class="error" v-else-if="!$v.promotion.discount.discountModule">Valores deben de ser multiplos de 5. Ex: 2.5.</span>
      </div>
    </div>

    <div>
      <label>Fecha de inicio de la promoción</label>
      <v-date-picker
        :masks="{ title: 'MMM YYYY' }"
        locale="es"
        v-model="promotion.date_init"
        :model-config="{
          type: 'string',
          mask: 'YYYY-MM-DD 00:00:00',
        }"
      >
        <template v-slot="{ inputValue, togglePopover }">
          <input
            :value="inputValue"
            placeholder="Ejemplo: 22/05/2022"
            readonly
            @focus="togglePopover"
            @blur="togglePopover"
          />
        </template>
      </v-date-picker>
      <div v-if="$v.promotion.discount.$dirty">
        <span class="error" v-if="!$v.promotion.date_init.required">Este campo es obligatorio.</span>
        <span class="error" v-else-if="!$v.promotion.date_init.minValue">La fecha de inicio debe ser mínimo 15 dias en el futuro.</span>
      </div>
    </div>

    <div>
      <label>Fecha de fin de la promoción</label>
      <v-date-picker
        locale="es"
        v-model="promotion.date_end"
        :model-config="{
          type: 'string',
          mask: 'YYYY-MM-DD 23:59:59',
        }"
      >
        <template v-slot="{ inputValue, togglePopover }">
          <input
            placeholder="Ejemplo: 22/05/2022"
            :value="inputValue"
            readonly
            @focus="togglePopover"
            @blur="togglePopover"
          />
        </template>
      </v-date-picker>
      <div v-if="$v.promotion.date_end.$dirty">
        <span class="error" v-if="!$v.promotion.date_end.required">Este campo es obligatorio.</span>
        <span class="error" v-else-if="!$v.promotion.date_end.minValue">La fecha de inicio debe ser mínimo 15 dias en el futuro con respecto a la fecha de inicio.</span>
      </div>
    </div>
    <button type="submit">Crear promoción</button>
  </form>
</template>

<script>
import { mapActions, mapState } from 'vuex'
import { required, between } from 'vuelidate/lib/validators'

const discountModule = (discount) => (Math.round(discount * 100) % 5 === 0)
let actualMinDate = new Date()

export default {
  data () {
    return {
      promotion: {
        date_init: null,
        date_end: null,
        product: null,
        discount: null,
        rating: 0 // Left rating set to default value for new promotions.
      }
    }
  },
  watch: {
    date_init (value) {
      if (value === null) {
        return
      }
      actualMinDate = value
    }
  },
  validations: {
    promotion: {
      product: {
        required
      },
      discount: {
        required,
        between: between(0.05, 99.95),
        discountModule
      },
      date_init: {
        required,
        minValue: value => {
          const date = new Date()
          const result = date.setDate(date.getDate() + 15)
          return value >= new Date(result).toISOString()
        }
      },
      date_end: {
        required,
        minValue: value => {
          const date = new Date(actualMinDate)
          const result = date.setDate(date.getDate() + 15)
          return value >= new Date(result).toISOString()
        }
      }
    }
  },
  computed: {
    ...mapState('promotions', ['products']),
    date_init () {
      return this.promotion.date_init
    }
  },
  methods: {
    ...mapActions('promotions', ['fetchProducts', 'postNewPromotion']),
    submit () {
      this.$v.$touch()
      if (this.$v.$invalid) {
        console.log('ERROR')
      } else {
        this.postNewPromotion(this.promotion)
      }
    }
  },
  created () {
    this.fetchProducts()
  }
}
</script>

<style lang="scss" scoped>
form {
  width: 400px;
}
button[type="submit"]{
  margin-top: 30px;
}

.form-group {
  &--error {
    background-color: red;
  }
}

.error {
  color: red;
}
</style>

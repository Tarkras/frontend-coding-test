<template>
  <div class="card">
    <div class="card__images_container">
      <img class="card__product-image" :src="promotion.product.image" />
      <img class="card__promotion-image" :src="dicountImgSrc"/>
    </div>
    <div class="card__body">
      <div class="card__ratings">
        <div v-for="(rating, index) in promotion.rating" :key="index">
          <img :src="fullStar"/>
        </div>
        <div v-for="(rating, index) in (fullRating - promotion.rating)" :key="promotion.rating + index">
          <img :src="emptyStar"/>
        </div>
      </div>
      <h4 class="card__title">{{promotion.product.name}}</h4>
      <div><p class="card__text">{{promotion.product.description}}</p></div>

      <div><p class="card__promotion-dates">Validez: {{formatDate(promotion.date_init)}} - {{formatDate(promotion.date_end)}}</p></div>
    </div>
  </div>
</template>

<script>
import fullStar from '@/assets/icons/full-star.png'
import emptyStar from '@/assets/icons/empty-star.png'
export default {
  data () {
    return {
      fullStar,
      emptyStar,
      fullRating: 5,
      dateOptions: {
        year: 'numeric',
        month: '2-digit',
        day: '2-digit'
      }
    }
  },
  props: {
    promotion: {
      type: Object,
      required: true
    }
  },
  methods: {
    formatDate (date) {
      const newDate = new Date(date)
      return new Intl.DateTimeFormat('es', this.dateOptions).format(newDate)
    }
  },
  computed: {
    dicountImgSrc () {
      let discountCode
      const discountStr = this.promotion.discount.toFixed(2).replace('.', '')
      if (this.promotion.discount < 10) {
        discountCode = `0${discountStr}`
      } else {
        discountCode = `${discountStr}`
      }
      return `https://public-storage-cdn-farmapremium.imgix.net/discount-icons/disc_gen_es_${discountCode}.png?w=110`
    }
  }
}
</script>

<style lang="scss" scoped>
.card{
  border: 1px solid #444;
  margin-bottom: 20px;
  max-width: 100%;
  border: 1px solid rgba(197, 199, 205, 0.3);
  border-radius: 6px;

  @media (min-width: 650px) {
    margin-bottom: 50px;
    max-width: calc(50% - 1.5em);
  }

  @media (min-width: 1000px) {
    margin-bottom: 50px;
    max-width: calc(33% - 1.5em);
  }

  &__images_container {
    position: relative;
    background: #FAFAFA;
    border-bottom: 0.5px solid #E2E2E2;
  }

  &__body {
    padding: 20px 25px;
  }

  &__product-image {
    max-width: 335px;
    margin: auto;
    display: block;

    @media (min-width: 650px) {
      max-width: 265px;
    }

    @media (min-width: 780px) {
      max-width: 335px;
    }

    @media (min-width: 1100px) {
      max-width: 292px;
    }

    @media (min-width: 1200px) {
      max-width: 360px;
    }
  }

  &__promotion-image {
    position: absolute;
    top: 29px;
    left: 22px;
  }

  &__title {
    margin-bottom: 5px;
  }

  &__text {
    margin-bottom: 8px;
  }

  &__ratings {
    display: flex;
    margin-right: 3px;
    margin-bottom: 13px;
  }

  &__promotion-dates {
    color: #019097;
  }
}
.product-image{
  width: 200px;
}
</style>

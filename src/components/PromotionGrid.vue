<template>
  <div>
    <div class="promotions-grid">
      <div class="pagination">
        <paginate-buttons
          :page-count="numePages"
          :click-handler="onPageClick"
          :prev-text="'<'"
          :next-text="'>'"
          :container-class="'pagination__container'"
          :active-class="'pagination__active'"
          :page-class="'pagination__list-item'"
          :page-link-class="'pagination__links'"
          :prev-link-class="'pagination__link--prev'"
          :next-link-class="'pagination__link--next'"
        >
        </paginate-buttons>
      </div>
      <div class="promotions-grid__card-container">
        <promtion-card
          v-for="promotion in promotions"
          :key="promotion.id"
          :promotion="promotion"
        ></promtion-card>
      </div>
    </div>
  </div>
</template>

<script>
import PromtionCard from '@/components/PromotionCard'
const PROMO_PER_PAGE = 9
export default {
  components: { PromtionCard },
  data () {
    return {
      page: 1
    }
  },
  props: {
    promotions: {
      type: Array,
      required: true
    },
    sortValue: {
      type: Object,
      default: () => {
        return {}
      }
    },
    filterValue: {
      type: Object,
      default: () => {
        return {}
      }
    }
  },
  methods: {
    onPageClick (selectedPage) {
      this.page = selectedPage
    }
  },
  computed: {
    numePages () {
      return Math.ceil(this.promotions.length / PROMO_PER_PAGE)
    }
  }
}
</script>

<style lang="scss">
.promotions-grid {
  max-width: 100%;
  height: 100%;

  &__card-container {
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    justify-content: space-between;
    gap: 20px;
  }
}
.pagination{
  color: #B1B1B1;

  &__container {
    display: flex;
    margin: 22px auto;
    width: 200px;
    justify-content: space-around;

    @media (min-width: 650px) {
      margin: 30px auto;
    }
  }

  &__active {
    background-color: #00B0B9;
    border-radius: 50px;
    color: white !important;
  }

  &__list-item {
    width: 20px;
    height: 20px;
    text-align: center;
  }

  &__links {
    color: #B1B1B1;
  }

  &__link--next, &__link--prev {
    width: 4px;
    height: 6px;
    color: #B1B1B1;
  }
}
</style>

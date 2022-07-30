<template>
  <div>
    <div class="promotions-grid">
      <div class="pagination">
        <paginate-buttons
          :page-count="numePages"
          :page-range="5"
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
          v-for="promotion in filterPromotions"
          :key="promotion.id"
          :promotion="promotion"
        ></promtion-card>
      </div>
    </div>
  </div>
</template>

<script>
import PromtionCard from '@/components/PromotionCard'

export default {
  components: { PromtionCard },
  data () {
    return {
      page: 1,
      innerWidth: window.innerWidth,
      totalPromotionsPerPage: null
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
  created () {
    this.calculateTotaltotalPromotionsPerPage()
  },
  mounted () {
    window.addEventListener('resize', this.calculateTotaltotalPromotionsPerPage)
  },
  methods: {
    onPageClick (selectedPage) {
      this.page = selectedPage
    },
    sortPromotions (promotions) {
      if (this.sortValue === null) {
        return promotions
      }

      switch (this.sortValue.value.value) {
        case 'asc':
          return promotions.sort((a, b) => a[this.sortValue.value.field] - b[this.sortValue.value.field])
        case 'desc':
          return promotions.sort((a, b) => b[this.sortValue.value.field] - a[this.sortValue.value.field])
        default:
          return promotions
      }
    },
    calculatePageElements (filteredValue) {
      return filteredValue.slice((this.page - 1) * this.totalPromotionsPerPage, this.page * this.totalPromotionsPerPage)
    },
    calculateTotaltotalPromotionsPerPage () {
      this.innerWidth = window.innerWidth
      if (this.innerWidth >= 1000) {
        this.totalPromotionsPerPage = 9
      } else if (this.innerWidth >= 650) {
        this.totalPromotionsPerPage = 6
      } else {
        this.totalPromotionsPerPage = 4
      }
    }
  },
  computed: {
    numePages () {
      return Math.ceil(this.promotions.length / this.totalPromotionsPerPage)
    },
    filterPromotions () {
      let filteredValue = []

      if (this.filterValue === null) {
        filteredValue = this.sortPromotions(this.promotions)
        return this.calculatePageElements(filteredValue)
      }

      switch (this.filterValue.value.type) {
        case '>':
          filteredValue = this.sortPromotions(this.promotions
            .filter(promotion => promotion[this.filterValue.value.field] > this.filterValue.value.value))
          break
        case '<':
          filteredValue = this.sortPromotions(this.promotions
            .filter(promotion => promotion[this.filterValue.value.field] < this.filterValue.value.value))
          break
        default:
          filteredValue = this.sortPromotions(this.promotions)
          break
      }

      return this.calculatePageElements(filteredValue)
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
    margin-bottom: 60px;

    @media (min-width: 650px) {
      margin-bottom: 0;
    }
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

    a {
      color: white;
    }
  }

  &__list-item {
    width: 20px;
    height: 20px;
    text-align: center;

    a {
      font-size: 13px;
      line-height: 20px;
    }
  }

  &__links {
    color: #B1B1B1;
  }

  &__link--next {
    width: 4px;
    height: 6px;
    color: #B1B1B1;
  }

  &__link--prev {
    width: 4px;
    height: 6px;
    color: #B1B1B1;
  }
}
</style>

<style lang="scss" scoped>
  .pagination__active {
    .pagination__links {
      color: red;
    }
  }
</style>

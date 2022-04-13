<template>
  <div class="rating">
    <span class="dimension">{{dimension}} ({{count}})</span>
    <span class="stars">
      <star-rating :rating="average" :increment="0.01" :star-size="30" :read-only="true"/>
    </span>
    </div>
</template>

<script>
import StarRating from 'vue-star-rating'

export default {
  name: 'RatingComponent',
  props: {
    dimension: {
      type: String,
      required: true
    },
    count: {
      type: [String, Number],
      required: true
    },
    average: {
      type: Number,
      required: true
    }
  },
  components: {
    StarRating
  }
}
</script>]

<style scoped lang="scss">
.rating {
  .dimension {
    display: block;
    text-align: left;
  }
}
</style>

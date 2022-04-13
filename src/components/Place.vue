<template>
  <div class="place">
    <h3 class="place-name" v-if="place">{{place.displayed_what}}</h3>
    <div class="place-info" v-if="place">
      <div class="address-holder">
        <span class="place-address">{{place.displayed_where}}</span>
        <div class="place-contacts">
          <span>Contacts:</span>
          <a :href="`tel:${contact.call_link}`" class="phone" v-for="contact in contacts" :key="contact.call_link">{{contact.call_link}}</a>
        </div>
        <span class="place-map">
          <GmapMap
            :center="place.location"
            :zoom="16"
            map-type-id="roadmap"
            style="width: 500px; height: 300px"
          >
            <GmapMarker
              :position="place.location"
              :clickable="false"
              :draggable="false"
            />
          </GmapMap>
        </span>
      </div>
      <div class="opening-hours">
        <div class="title-holder" @click="showHours = !showHours">
          <span class="title">Opening Hours</span>
          <span class="arrow" :class="{top: showHours, bottom: !showHours}"></span>
        </div>
        <transition name="slide">
          <div class="days-holder" v-if="showHours">
            <div class="day" v-for="(day, index) in place.opening_hours" :key="`day-${index}`">
              <div class="day-left">{{day.day}}</div>
              <div class="day-right">
                <span class="hours closed" v-if="day.type && day.type === 'CLOSED'">
                  {{day.type}}
                </span>
                <span class="hours" v-for="(hour, index) in day.hours" :key="`hour-${index}`">{{hour.start}} - {{hour.end}}</span>
              </div>
            </div>
          </div>
        </transition>
      </div>
      <div class="ratings">
        <div class="title-holder" @click="showRatings = !showRatings">
          <span class="title">Ratings</span>
          <span class="arrow" :class="{top: showRatings, bottom: !showRatings}"></span>
        </div>
        <transition name="slide">
          <div class="ratings-holder" v-if="showRatings">
            <rating v-for="(rating, index) in ratings" :key="`rating-${index}`" :dimension="rating.dimension" :average="rating.average" :count="rating.count"></rating>
            <span class="main-ratings-count">From {{place.ratings_count}} votes</span>
          </div>
        </transition>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import Rating from './Rating.vue'

export default {
  name: 'PlaceComponent',
  data() {
    return {
      place: null,
      showHours: true,
      showRatings: true
    }
  },
  components: {
    Rating
  },
  watch: {
    '$route.params': {
      handler() {
        this.fetchData(this.$route.params.id)
        this.showHours = true
        this.showRatings = true
      },
      immediate: true,
    }
  },
  computed: {
    ratings() {
      return this.place.rating_summaries.filter(rating => rating.display)
    },
    contacts() {
      return this.place.contacts.filter(contact => contact.contact_type === 'phone')
    }
  },
  methods: {
    fetchData() {
      axios.get(`http://localhost:3000/places/${this.$route.params.id}`).then(response => {
        if (response.data.status === "OK") {
          this.place = response.data.place
        }
      }).catch(error => {
        console.log('error on getting place', error)
      })
    }
  }
}
</script>

<style scoped lang="scss">
.place {
  
  .place-info {
    display: flex;
    flex-direction: column;
    max-width: 500px;
    margin: 0 auto;
    .address-holder {
      display: flex;
      flex-direction: column;
      justify-content: flex-start;
      align-items: baseline;

      .place-address {
        margin-bottom: 8px;
        font-weight: bold;
      }

      .place-contacts {
        span {
          margin-right: 8px;
        }
        a {
          margin-right: 8px;
          text-decoration: none;
        }
      }
    }

    .opening-hours {
      margin-top: 24px;
      .title-holder {
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-bottom: 8px;
        cursor: pointer;
        .title {
          display: block;
          font-weight: bold;
          text-align: left;
        }
      }

      .days-holder {
        min-width: 300px;

        .day {
          display: flex;
          justify-content: space-between;

          margin-bottom: 8px;

          &:last-child {
            margin-bottom: 0px;
          }

          .day-left {

          }

          .day-right {
            display: flex;
            flex-direction: column;

            .hours {
              text-transform: lowercase;

              &.closed {
                color: red;
              }
            }
          }
        }
      }
    }

    .ratings {
      margin-top: 24px;

      .title-holder {
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-bottom: 8px;
        cursor: pointer;
        .title {
          display: block;
          font-weight: bold;
          text-align: left;
        }
      }

      .main-ratings-count {
        font-size: 12px;
        text-align: left;
        display: block;
        color: #999999;
      }
    }
  }
}

.arrow {
  display: block;
  width: 16px;
  height: 16px;
  cursor: pointer;
  &.top {
    background-image: url('https://img.icons8.com/external-those-icons-lineal-color-those-icons/24/000000/external-arrows-arrows-those-icons-lineal-color-those-icons-15.png');
    background-position: center;
    background-repeat: no-repeat;
  }

  &.bottom {
    background-image: url('https://img.icons8.com/external-those-icons-lineal-color-those-icons/24/000000/external-arrow-arrows-those-icons-lineal-color-those-icons-1.png');
    background-position: center;
    background-repeat: no-repeat;
  }
}

.slide-enter-active,
.slide-leave-active {
  transition: max-height 0.3s ease-in-out;
}

.slide-enter-to, .slide-leave {
   max-height: 400px;
   overflow: hidden;
}

.slide-enter, .slide-leave-to {
   overflow: hidden;
   max-height: 0;
}
</style>

<template>
  <div class="menu">
    <ul>
      <li
        v-for="({ item, price }, index) in shops[selectIndex].menu"
        :key="index"
      >
        <div class="menu__text">
          <span>{{ item }}</span>
          <span>¥{{ price }}</span>
        </div>
        <div @click="toggleMenuItem(index, item, price)">
          <button-grass :class="'menu-btn adder-' + index" />
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
import { mapState, mapGetters } from 'vuex'
// import { Expo } from 'gsap'
import _ from 'lodash'
import ButtonGrass from './ButtonGrass.vue'

export default {
  components: { ButtonGrass },
  data() {
    return {
      menuItemAdded: []
    }
  },
  computed: {
    ...mapState(['shops', 'page', 'cart']),
    ...mapGetters(['selectIndex', 'cartQty'])
  },
  created() {
    for (let i = 0; i < this.shops[this.selectIndex].menu.length; i++) {
      this.menuItemAdded[i] = false
    }
  },
  mounted() {
    this.isCarted()
  },
  methods: {
    isCarted() {
      this.cart.forEach((cartBody) => {
        this.shops[this.selectIndex].menu.forEach((teaBody, teaIndex) => {
          if (_.isEqual(cartBody, teaBody)) {
            this.menuItemAdded[teaIndex] = true
            this.redShake(teaIndex, 'forward')
          }
        })
      })
    },
    toggleMenuItem(index, i, p) {
      if (!this.menuItemAdded[index]) {
        this.addTeaItem(index, i, p)
      } else {
        this.removeTeaItem(index, i)
      }
      this.menuItemAdded[index] = !this.menuItemAdded[index]
    },
    redShake(index, direction) {
      let TimelineMax, Expo
      if (process.browser) {
        const gsap = require('gsap')
        TimelineMax = gsap.TimelineMax
        Expo = gsap.Expo
      }

      const tl = new TimelineMax()
      if (direction === 'forward') {
        tl.to('.adder-' + index, 0.1, { x: '+=20', yoyo: true, repeat: 5 }, '<')
        tl.to(
          '.adder-' + index,
          0.1,
          { x: '-=20', yoyo: true, repeat: 5 },
          '<0.1'
        )
        tl.to(
          '.adder-' + index + ' #vector3',
          1,
          { opacity: 0, ease: Expo.easeInOut },
          '<'
        )
        tl.to(
          '.adder-' + index + ' #btngrass',
          1,
          { opacity: 0, ease: Expo.easeInOut },
          '<'
        )
        tl.to(
          '.adder-' + index + ' #btnbkgnd',
          1,
          { opacity: 0.8, fill: '#ad2e1b', ease: Expo.easeInOut },
          '<'
        )
        tl.to(
          '.adder-' + index + ' #vector2',
          1,
          { stroke: '#333', ease: Expo.easeInOut },
          '<'
        )
      }
      if (direction === 'reverse') {
        tl.to('.adder-' + index, 0.1, { x: '-=20', yoyo: true, repeat: 5 }, '<')
        tl.to(
          '.adder-' + index,
          0.1,
          { x: '+=20', yoyo: true, repeat: 5 },
          '<0.1'
        )
        tl.to(
          '.adder-' + index + ' #vector3',
          1,
          { opacity: 1, ease: Expo.easeInOut },
          '<'
        )
        tl.to(
          '.adder-' + index + ' #btngrass',
          1,
          { opacity: 1, ease: Expo.easeInOut },
          '<'
        )
        tl.to(
          '.adder-' + index + ' #btnbkgnd',
          1,
          { opacity: 1, fill: '#49412B', ease: Expo.easeInOut },
          '<'
        )
        tl.to(
          '.adder-' + index + ' #vector2',
          1,
          { stroke: '#83744E', ease: Expo.easeInOut },
          '<'
        )
      }
    },
    addTeaItem(index, i, p) {
      this.redShake(index, 'forward')
      this.$store.commit('addToCart', { item: i, price: p })
    },
    removeTeaItem(index, i) {
      this.redShake(index, 'reverse')
      this.$store.commit('removeFromCart', i)
    }
  }
}
</script>

<style>
.menu {
  display: grid;
  place-items: center;
}

li {
  list-style-type: none;
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin: 5px 0;
  line-height: 0.7em;
}

ul {
  width: 80%;
}
.menu__text {
  display: flex;
  width: 80%;
  /* margin-right: 8px; */
  justify-content: space-between;
}

.menu__text > * {
  margin: auto 5px;
}

@keyframes shake {
  0% {
    transform: translateY(0);
  }
  12% {
    transform: translateY(100px);
  }
  25% {
    transform: translateY(-75px);
  }
  50% {
    transform: translateY(50px);
  }
  75% {
    transform: translateY(-25px);
  }
  100% {
    transform: translateY(0);
  }
}
</style>

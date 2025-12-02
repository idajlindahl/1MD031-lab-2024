<template>
  <div class="burger">
    <h3>{{ burger.name }} ({{ burger.kCal }} kCal)</h3>
      <img v-bind:src="burger.url" :alt="'Image of' + burger.name" class="burgerimg">
      <h4>Ingredients</h4>
      <ul>
        <li v-for="ingredient in burger.ingredients" :key="ingredient">
          {{ ingredient }}
        </li>
      </ul>
      <h4>Allergens</h4>
      <ul>
        <li v-for="allergen in burger.allergens" :key="allergen">
          {{ allergen }}
        </li>
      </ul>
      <h4>Amount ordered: {{ amountOrdered }}</h4>
      <div>
        <button v-on:click="decreaseAmount">-</button>
        <button v-on:click="increaseAmount">+</button>
      </div>
  </div>
</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },
  data: function () {
    return {
      amountOrdered: 0,
    }
  },

  methods: {
    increaseAmount: function () {
      this.amountOrdered++;
      this.emitOrder();
    },
    decreaseAmount: function () {
      if (this.amountOrdered > 0) {
        this.amountOrdered--;
        this.emitOrder();
      }
    },
    emitOrder: function () {
      this.$emit('orderedBurger', {
        name: this.burger.name,
        amount: this.amountOrdered
      });
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.burger {
    margin: 20px;
    padding: 10px;
    background-color: darkred;
    border-radius: 5px;
}

.burgerimg {
    width: 90%;
    
}

.allergen {
    font-weight: bold;
} 

</style>
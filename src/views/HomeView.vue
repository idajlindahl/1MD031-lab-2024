<template>

  <header>
      <div id="header-section">
        <img src="/img/Burgershop.png" alt="Burgershop.png" id="header-img"/>
        <h1 id="header-h1">Welcome to BurgerShop</h1>
      </div>
  </header>
  
  <main>
    <section id="menu-section">
      <h2>Menu</h2>
      <h4>Select burger</h4>
        <div class="burgeroptions">
          <Burger v-for="burger in burgers" 
                  v-bind:burger="burger" 
                  v-bind:key="burger.name" 
                  v-on:orderedBurger="addToOrder($event)"/>
        </div>
    </section>

    <section id="orderinfo-section">
      <h2>Customer information</h2>
      <h3>Provide necessary information for your delivery</h3>
        <p>
          <label for="fullname">Full Name</label><br>
          <input type="text" id="fullname" v-model="fn" required="required" placeholder="First- and Last name">
        </p>
        <p>
          <label for="em">E-mail</label><br>
          <input type="email" id="email" v-model="em" required="required" placeholder="E-mail address">
        </p>

        <p>
          <label for="payment">Payment options</label><br>
          <select id="payment" v-model="po">
            <option>Swish</option>
            <option>Debit Card</option>
            <option>Credit Card</option>
            <option>Apple Pay</option>
            <option>Klarna</option>
          </select>
        </p>

        <p>
          <label for="gender">Gender</label><br>
          <div> 
            <input type="radio" id="gender_female" v-model="rb" value="female" checked/>
            <label for="gender">Female</label>
          </div>
          <div> 
            <input type="radio" id="gender_male" v-model="rb" value="male"/>
            <label for="gender">Male</label>
          </div>
          <div> 
            <input type="radio" id="gender_nonbin" v-model="rb" value="nonbin"/>
            <label for="gender">Non-binary</label>
          </div>
          <div> 
            <input type="radio" id="gender_undis" v-model="rb" value="undis"/>
            <label for="gender">Undisclosed</label>
          </div>
        </p>
      <h3>Indicate point of delivery</h3>
      <div id="map-container">
        <div id="map" v-on:click="setLocation">
          <div id="target" v-bind:style="{
            left: location.x + 'px',
            top: location.y + 'px'
          }">📍</div>
        </div>
      </div>
    </section>

      

      <button type="submit" id="submit-button" v-on:click="submitOrder">
        <img src="/img/youdidit.jpg" alt="Send Order" style="width: 30px">
        Send Order
      </button>
  </main>

  <footer>
    <hr>
    <p>&copy; 2003 Fake Burgers AB</p>
  </footer>
</template>


<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io("localhost:3000");

function MenuItem(name, kCal, imageURL,  hasGluten, hasLactose) {
  this.name = name;
  this.kCal = kCal;
  this.image = imageURL;
  this.gluten = hasGluten;
  this.lactose = hasLactose;
}

const allBurgers = menu;


export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: allBurgers,
      fn: '',
      em: '',
      st: '',
      hs: null,
      po: 'Swish',
      rb: 'Female',
      orderedBurgers: {},
      location: {
        x: 0,
        y: 0
      }
    }
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    setLocation: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};

      const x = event.clientX - 10 - offset.x;
      const y = event.clientY - 10 - offset.y;

      this.location.x = x;
      this.location.y = y;
      },

    addOrder: function () { 

      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: this.location.x,
                                           y: this.location.y,
                                          fullName: this.fn,
                                          email: this.em,
                                          payment: this.po,
                                          gender: this.rb},
                                orderItems: this.orderedBurgers
                              }
                 );
    },

    addToOrder: function (event) {
      console.log("Order received", event);
      this.orderedBurgers[event.name] = event.amount;
      console.log("Current order", this.orderedBurgers);

    },

    submitOrder: function () {
      console.log("Order Information");
      console.log("Full Name:", this.fn);
      console.log("E-mail:", this.em);
      console.log("Street:", this.st);
      console.log("House Number:", this.hs);
      console.log("Payment Method:", this.po);
      console.log("Gender:", this.rb);
      console.log("Ordered burgers");
      console.log(this.orderedBurgers);
      this.addOrder();
    }
  }
}
</script>

<style>

#map-container {
  width: 650px;
  height: 300px;
  overflow: scroll;
}

#map {
  width: 1920px;
  height: 1078px;
  background: url("/img/polacks.jpg");
  position: relative;
}

#target {
  position: absolute;
  width: 20px;
  height: 20px;
}

@import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');
body {
    font-family: 'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial, sans-serif;
    font-size: 16px;
}

section {
    margin: 10px 10px;
}

.burgeroptions {
    display: grid;
    grid-gap: 10px;
    grid-template-columns: 33.33% 33.33% 33.33%;
}

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

#header-section {
    margin: 10px 15px;
    height: 200px;
    overflow:hidden;
    position: relative;
}

#header-img {
    opacity: 0.7;
    width: 100%;
    height: auto;
}

#header-h1 {
    position: absolute;
    top: 30%;
    left: 5%;
    color: darkred;
}


#menu-section {
    background-color: #000000;
    color: #ffffff;
    border: 5px dotted #ffffff;
    padding: 5px 5px 5px 5px;
}

#menu-section h2,  
#menu-section h4 {
    grid-column: 1/-1;
}

#orderinfo-section {
    border: 5px dotted #000000;
    padding: 5px 5px 5px 20px;
}

#submit-button {
    margin: 20px 0px 0px 15px;
}

button:hover {
    background-color: #ff0000;
    cursor: pointer;
}


</style>
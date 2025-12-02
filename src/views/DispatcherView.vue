<template>
    <div id="orders">
      <div id="orderList">
        <div v-for="(order, key) in orders" v-bind:key="'order'+key">
          #{{ key }}: {{ makeToList(order.orderItems) }}:
          <br>{{ order.details.fullName }}, {{ order.details.email }}, {{ order.details.payment }}, {{ order.details.gender }}</br>
        </div>
        <button v-on:click="clearQueue">Clear Queue</button>
      </div>
      <div id="dots">
          <div v-for="(order, key) in orders" v-bind:style="{ left: order.details.x + 'px', top: order.details.y + 'px'}" v-bind:key="'dots' + key">
            {{ key }}
          </div>
      </div>
    </div>
  </template>
  <script>
  import io from 'socket.io-client'
  const socket = io("localhost:3000");
  
  export default {
    name: 'DispatcherView',
    data: function () {
      return {
        orders: {},
      }
    },
    created: function () {
      socket.on('currentQueue', data =>
        this.orders = data.orders);

      socket.on('addOrder', (order) => {
        this.$set(this.orders, order.orderId, order);
      })

    },
    methods: {
      clearQueue: function () {
        socket.emit('clearQueue');
      },
      changeStatus: function(orderId) {
        socket.emit('changeStatus', {orderId: orderId, status: "Annan status"});
      },
      makeToList: function(itemsObject) {
        let itemsArray = [];
        for (const [name, amount] of Object.entries(itemsObject)) {
          if (amount > 0) {
            itemsArray.push(`${amount} x ${name}`);
          }
        }
        return itemsArray.join(", ");
      }
    }
  }
  </script>
  <style>
  #orderList {
    top:1em;
    left:1em;
    position: absolute;
    z-index: 2;
    color:black;
    background: rgba(255,255,255, 0.5);
    padding: 1em;
  }
  #dots {
    position: relative;
    margin: 0;
    padding: 0;
    background-repeat: no-repeat;
    width:1920px;
    height: 1078px;
    cursor: crosshair;
    background-image: url('/img/polacks.jpg');
  }
  
  #dots div {
    position: absolute;
    background: black;
    color: white;
    border-radius: 10px;
    width:20px;
    height:20px;
    text-align: center;
  }
  </style>
  
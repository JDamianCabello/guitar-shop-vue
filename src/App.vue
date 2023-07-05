<script setup>
  import { ref, watch, onMounted } from 'vue';
  import {db} from './data/guitars'
  import GuitarItem from './components/GuitarItem.vue'
  import Header from './components/Header.vue'
  import Footer from './components/Footer.vue'

  const guitars = ref([]);
  const headerGuitar = ref([]);
  const cart = ref([]);

  const addToCart = (item) => {
        const existItem = cart.value.findIndex(x => x.id === item.id)

        if(existItem >= 0){
          cart.value[existItem].quantity++;
        }else{
          item.quantity = 1;
          cart.value.push(item);
        }
    }

  const addItemQuantity = (item) => cart.value.find(x => x.id === item.id).quantity++
  const substracItemQuantity = (item) => cart.value.find(x => x.id === item.id).quantity--
  const deleteItem = (item) => cart.value = cart.value.filter(x => x.id !== item.id)

  const cleanCart = () => cart.value = []

  const saveLocalStore = () => localStorage.setItem('cart', JSON.stringify(cart.value));

  watch(cart, () => {
      saveLocalStore()
  },{
    deep:true
  })
    
  onMounted(() => {
    guitars.value = db
    headerGuitar.value = db[3]

    const savedCart = localStorage.getItem('cart')
    if(savedCart){
      cart.value = JSON.parse(savedCart)
    }
  })
</script>

<template>
    <Header 
    :cartData="cart"
    :headerGuitar="headerGuitar"
    @add-quantity="addItemQuantity"
    @substrac-quantity="substracItemQuantity"
    @add-to-cart-header="addToCart"
    @delete-item="deleteItem"
    @clean-cart="cleanCart"
     />

    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>
        <div class="row mt-5">
          <GuitarItem 
          v-for="guitar in guitars" :key="guitar.id"
          :guitar="guitar"
          @add-to-cart="addToCart"
          />
        </div>
    </main>

    <Footer/>
</template>

<style lang="scss" scoped>

</style>
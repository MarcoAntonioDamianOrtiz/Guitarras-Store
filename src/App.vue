<script setup>

import { ref, reactive, computed, watch } from 'vue'
import { db } from './data/guitarras'
import Header from './components/Header.vue'
import Footer from './components/Footer.vue'
import Guitarra from './components/Guitarra.vue'
const guitarras = ref(db)
const carrito =  ref([])

function agregarCarrito(guitarra) {
    const idCarrito = carrito.value.findIndex(g => g.id === guitarra.id)
    if (idCarrito === -1) {
        guitarra.cantidad = 1
        carrito.value.push(guitarra)
    } else {
        carrito.value(idCarrito).cantidad++;
    }
}

function agregaUno(id){
    const idCarrito = carrito.value.findIndex(g => g.id === id)
    carrito.value[idCarrito].cantidad++
}

function quitaUno(id){
    const idCarrito = carrito.value.findIndex(g => g.id === id)
    if (carrito.value[idCarrito].cantidad > 1) {
        carrito.value[idCarrito].cantidad--
    }
}

function eliminaGuitarra(id) {
    carrito.value = carrito.value.filter(g => g.id !== id);
}

const vaciarCarrito = () => { carrito.value = [] }

const total = computed(() => {
    let total = 0
    carrito.value.forEach(g => {
        total += g.cantidad * g.precio
    })
    return total
})

watch(carrito, total)
</script>

<template>

    <Header
        :carrito="carrito"
        :total="total"
        @agrega-uno="agregaUno"
        @quita-uno="quitaUno"
        @elimina-guitarra="eliminaGuitarra"
        @vaciar-carrito="vaciarCarrito"
    />

    <main class="container-xl mt-5">
        
        <h2 class="text-center">Nuestra Colección</h2>
        <div class="row mt-5">

        <Guitarra
            v-for="guitarra in guitarras"
            :guitarra="guitarra"
            :key="guitarra.id"
            @agregar-carrito="agregarCarrito"
        />
        </div>
    </main>


    <Footer/>


</template>

<style scoped>

</style>
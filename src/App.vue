<script setup>

import { ref, reactive, computed, onMounted, watch } from 'vue'
import { db } from './data/guitarras'
import Header from './components/Header.vue'
import Footer from './components/Footer.vue'
import Guitarra from './components/Guitarra.vue'

const guitarras = ref([])
const guitarra = ref({})
const carrito =  ref([])

onMounted(() => {
    guitarras.value = db
    const carritoStorage = localStorage.getItem('carrito')
    if (carritoStorage) {
        carrito.value = JSON.parse(carritoStorage)
    }
    guitarra.value = db[3]
})

function guardarLocalStorage() {
    localStorage.setItem('carrito',
        JSON.stringify(carrito.value)
    )
}

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
    return carrito.
            value.
            reduce((t, g) => t + (g.precio * g.cantidad), 0)
})

watch(carrito, guardarLocalStorage, {deep:true})

</script>

<template>

    <Header
        :carrito="carrito"
        :total="total"
        :guitarra="guitarra"
        @agregar-carrito="agregarCarrito"
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
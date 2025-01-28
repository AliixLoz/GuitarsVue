<script setup>
import { ref, computed, onMounted, watch } from 'vue'
import Header from './components/Header.vue';
import Footer from './components/Footer.vue';
import Guitarra from './components/Guitarra.vue';
import { db } from './data/guitarras';

const guitars = ref(db)
const carrito = ref([])


onMounted(() => {
    carrito.value = recuperaStorage()
})

function guardaStorage(){
    localStorage.setItem('carrito', JSON.stringify(carrito.value))
}

function recuperaStorage(){
    const datosStorage = localStorage.getItem('carrito')
    return datosStorage ? JSON.parse(datosStorage):[]
}

const total = computed(() => {
    let total = 0
    carrito.value.forEach(guitar => {
        total += guitar.cantidad * guitar.precio
    })
    return total
})


function agregarCarrito(guitar) {
    //Si id existe en carrito incrementamos cantidad
    const id = carrito.value.findIndex(g => g.id === guitar.id)
    if (id === -1) {
        carrito.value.push({ ...guitar, cantidad: 1 })
    } else {
        carrito.value[id].cantidad++
    }
}

function agregaUno(id) {
    const index = carrito.value.findIndex(g => g.id === id);
    index !== -1 && carrito.value[index].cantidad++
}

function quitaUno(id) {
    const index = carrito.value.findIndex(g => g.id === id);
    index !== -1 && carrito.value[index].cantidad > 1 ? carrito.value[index].cantidad-- : carrito.value.splice(index, 1)
}

function vaciarCarrito() {
    carrito.value = []
}

function quitarGuitarra(id) {
    const index = carrito.value.findIndex(g => g.id === id);
    index !== -1 && carrito.value.splice(index, 1)
}

watch(carrito, guardaStorage, {deep: true})


</script>
<template>
    <Header :carrito="carrito" :total="total" :guitarvai="guitars[3]" @agregar-carrito="agregarCarrito"
        @agrega-uno="agregaUno" @quita-uno="quitaUno" @quita-guitarra="quitarGuitarra"
        @vaciar-carrito="vaciarCarrito" />

    <main class="container-xl mt-5">
        <h2 class="text-center">Mi Colección</h2>

        <div class="row mt-5">
            <Guitarra v-for="guitar in guitars" :key="guitar.id" :guitarra="guitar" @agregar-carrito="agregarCarrito" />
        </div>
    </main>

    <Footer />
</template>
<script setup>
    import { onMounted, ref, computed } from "vue"
    import axios from "axios"

    const emit = defineEmits(['descubrirPokemon'])

    const props = defineProps({
        pokemon: Object
    })

    const nombre = ref("")
    const imageUrl = ref("")
    const descubierto = ref(false)
    const mostrarPokemon = computed(() => {
        return descubierto.value
    })

    const onSubmit = e => {
        if ( !nombre.value.trim() ) {
            alert("Debe ingresar un nombre");
            return;
        }
        
        if ( props.pokemon.name !== nombre.value.trim() ) {
            alert(`El nombre "${nombre.value.trim()}" es incorrecto`)
            return;
        }
        emit('descubrirPokemon', 1);
        descubierto.value = true
    };

    onMounted(() => {
        const getPokemon = async () => {

          try {
            const { data } = await axios(props.pokemon.url)
            imageUrl.value = data.sprites.other.dream_world.front_default
          } catch (error) {
            console.log(error.message);
          }

        }
        getPokemon()
    })

</script>

<template>
    <div class="card">
        <div class="card__imagen">
            <img 
            :class="{escondido: !mostrarPokemon}"
            :src="imageUrl" :alt="`imagen ${pokemon.name}`">
        </div>
        
        <div class="card__body">
           <p v-if="mostrarPokemon"  class="card__name">
            {{ pokemon.name }}
           </p>

           <form 
           @submit.prevent="onSubmit"
           v-else class="card__form">
                <input 
                :style="{
                    width: '100%'
                }"
                v-model="nombre"
                name="nombre"
                id="nombre"
                type="text">

                <button class="submit">Descubrir</button>
            </form>
        </div>
    </div>
</template>


<style scoped>
    .card {
        width: 100px;
        margin: 0 auto;
    }
    .card__imagen {
        margin-bottom: .5rem;
    }
    .card__imagen img {
        height: 100px;
        width: 100px;
    }
    .card__form {
        display: flex;
        flex-direction: column;
        gap: .5rem;
        align-items: center;
    }
    .card__form button {
        padding: 0 1rem;
    }

    .escondido {
        filter: blur(5px) grayscale(100%);
    }

</style>
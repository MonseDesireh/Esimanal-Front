<template>
        <div class="h-screen overflow-auto bg-slate-200">
            <div
                class="z-20 fixed top-0 h-full w-full text-center overflow-auto"
                style="background-color:rgba(50,50,50,0.5)"
                v-if="showCreateNewModal"
            >
                <div class="mx-auto w-2/3 text-center shadow-lg bg-white mt-8 rounded-lg p-8 z-30">
                    <p class="py-2">INGRESE LA NUEVA NOTICIA</p>
                    <input 
                        v-model="titulo"
                        placeholder="Titulo"
                        type="text"
                        class="border-2 border-gray-500 block w-2/3 mx-auto rounded-lg mb-4" 
                    >
                    <textarea 
                        v-model="contenido" 
                        placeholder="Contenido"
                        id="" 
                        cols="30" 
                        rows="10"
                        class="border-2 border-gray-500 block w-2/3 mx-auto rounded-lg"
                    ></textarea>
                    <div>
                        Selecciona una categoria:
                        <select class="border-2 border-black rounded-lg m-4 w-[240px]" v-model="categoria_seleccionada">
                            <option v-for="categoria in categorias" :key="categoria" :value="categoria">{{ categoria.descripcion }}</option>
                        </select>
                    </div>
                    <div>
                        <input type="file" @change="asignarImagen" accept=".jpg, .jpeg, .png">
                    </div>
                    <div>
                        <div
                            class="border-2 m-4 inline-block border-green-600 bg-green-500 hover:bg-green-600 cursor-pointer rounded-lg py-2 px-4"
                            @click="createNews"
                        > 
                            Aceptar 
                        </div>
                        <div 
                            @click="showCreateNewModal = false"
                            class="border-2 m-4 inline-block border-red-600 bg-red-500 hover:bg-red-600 cursor-pointer rounded-lg py-2 px-4"
                        >
                             Cancelar 
                        </div>
                    </div>
                </div>
            </div>
        <div class="sticky top-0 bg-white border-b-[5px] border-b-red-700 z-10">
            <img 
            src="../../../public/image/logoipn.png" 
            class="object-scale-down h-28 w-28 inline-block"
            >
            <div class="inline-block w-4/5 mx-auto">
                <p class="text-center letter text-emerald-700 text-8xl">
                    ESIMANAL
                </p> 
            </div>
            <img
                src="../../../public/image/logoesime.jpg" 
                class="object-scale-down h-28 w-28 flex float-right"
            >
        </div>
            
        <div class="inline-block m-10">
            <span 
                class="p-4 border-2 border-blue-600 bg-blue-500 hover:bg-blue-600 rounded-lg cursor-pointer"
                @click="showCreateNewModal = true"
            >
                Ingresar noticia
            </span>
        </div>

        <!-- {{ noticias[0] }} -->
        <!-- <img :src="noticias[0].img.ruta" alt=""> -->

        <div 
            class="border-solid bg-white border-[4px] border-red-800 text-center mt-10 w-3/5 rounded-2xl mx-auto p-[1px]"
            v-for="(noticia, index) in noticias" :key="index"
        >
            <div
                :style="{'background-image': `url('http://localhost:5000/get-image/${noticia.img.ruta}')`}"
                class="imagen rounded-t-xl z-0"
                :class="(!contenido_desplegado[index]) ? 'h-20' : 'h-40'"
            >
            </div>
            <div class="bg-slate-100">
                <strong class="text-center p-4">
                    <i>{{ noticia.categoria.descripcion }}</i>
                </strong>
            </div>
            <div class="">
                <strong class="text-lg text-left">{{ noticia.titulo }}</strong>
                <br>
                <p class="text-justify text-sm" v-if="!contenido_desplegado[index]">{{ noticia.contenido }}</p>
                <p class="text-right text-sm mt-4 mr-4">
                    <i>{{ noticia.autor.titulo }} : {{ noticia.autor.nombre }}</i>
                    <br>
                    <i>Contacto: {{ noticia.autor.contacto }}</i>
                </p>
                <!-- <p v-else>
                    {{ noticia.contenido }}
                </p> -->
                <!-- <i 
                    class="hover:underline underline-offset-1 hover:text-blue-500 text-black cursor-pointer text-right"
                    @click="contenido_desplegado[index] = !contenido_desplegado[index]"
                >
                    {{ (!contenido_desplegado[index]) ? 'Ver más...' : 'Ver menos...' }}
                </i> -->
            </div>
        </div>
    </div>
</template>



<script>
import axios from 'axios';

export default {
    data(){
        return {
            // Data para los datos que lleguen desde la DB
            contenido: '',
            logotipo: '',
            titulo: '',
            contenido_desplegado: [],
            categoria_seleccionada: {},
            categorias: {},
            noticias: {},
            showCreateNewModal: false,
            image: null
        };
    },
    mounted() {
        this.getNews();
        this.getCategorias();
    },
    methods: {
        asignarImagen($event) {
            this.image = $event.target.files[0];
            console.log(this.image);
        },
        async createNews() {
            const formData = new FormData();
            formData.append('photo', this.image);
            formData.append('titulo', this.titulo);
            formData.append('contenido',this.contenido)
            formData.append('categoria_id', this.categoria_seleccionada.id)

            const api = 'http://localhost:5000/post-news';
            const response = await axios.post(api, formData, {
                headers: {
                    'Content-Type': 'multipart/form-data'
                }
            });
            
            this.getNews();

            this.titulo = '';
            this.contenido = '';
            this.showCreateNewModal = false;
            this.categoria_seleccionada = {};
        },
        async getNews() {
            const api = 'http://localhost:5000/get-news';
            const response = await axios.get(api);
            this.noticias = response.data;
            console.log(this.noticias);
        },
        async getCategorias() {
            const api = "http://localhost:5000/get-categorias";
            const response = await axios.get(api);
            this.categorias = response.data;
        }
    },
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=DM+Serif+Display&display=swap');

.imagen {
  background-color: #cccccc;
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
  position: relative; /* Agregado para aplicar la fuente a elementos dentro de .imagen */
}

.letter {
    font-family: 'DM Serif Display', serif;
}
</style>

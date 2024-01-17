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

        <div 
            class="border-solid bg-white border-[4px] border-red-800 text-center mt-10 w-3/5 rounded-2xl mx-auto p-[1px]"
            v-for="(noticia, index) in noticias" :key="index"
        >
            <div
                :style="{'background-image': `url('../../../public/image/logoipn.png')`}" 
                class="imagen rounded-t-xl z-0"
                :class="(!contenido_desplegado[index]) ? 'h-20' : 'h-40'"
            ></div>
            <div class="">
                <strong class="text-lg text-left">{{ noticia.titulo }}</strong>
                <br>
                <p class="text-justify text-sm" v-if="!contenido_desplegado[index]">{{ noticia.contenido }}</p>
                <i>{{ noticia.autor.titulo }} : {{ noticia.autor.nombre }}</i>
                <br>
                <i>Contacto: {{ noticia.autor.contacto }}</i>
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
            data: [{}, {}],
            logotipo: '',
            contenido_desplegado: [],
            showCreateNewModal: false,
            titulo: '',
            contenido: '',
            noticias: {}
        };
    },
    mounted() {
        this.asignarImagen();
        this.getNews();
    },
    methods: {
        asignarImagen() {
            this.data[0].titulo = 'Titulo de prueba';
            this.data[0].pre = 'Omar García Harfuch pierde -ya saben, el ponderador es veleidoso- la encuesta por la candidatura de CDMX. Reconoce la decisión, o la cuota de género, y se pliega al dictado de Morena. Clara Brugada es virtual candidata, los que se oponían al policía celebran: la izquierda se ha salvado.';
            this.data[0].content = 'Claudia Sheinbaum invita a Omar a integrarse a los trabajos de diseño de -todavía no se puede decir con todas sus letras- la política anticrimen para todo el país. Se entiende que lo querrá de secretario de seguridad, de contrapeso a los generales, de su hombre contra la violencia. \n Qué dirán de esa invitación quienes desde el filomorenismo hoy cuestionan a García Harfuch por sus antecedentes familiares (¡?), su paso por la Policía Federal en tiempos de Peña Nieto (por supuesta carencia de exámenes de confianza, por Ayotzinapa, etc.), y porque no es de izquierda…';
            this.data[0].image = `../../../public/image/logo_esimanal.png`;
            this.data[1].titulo = 'Titulo de prueba';
            this.data[1].pre = 'Omar García Harfuch pierde -ya saben, el ponderador es veleidoso- la encuesta por la candidatura de CDMX. Reconoce la decisión, o la cuota de género, y se pliega al dictado de Morena. Clara Brugada es virtual candidata, los que se oponían al policía celebran: la izquierda se ha salvado.';
            this.data[1].content = 'Claudia Sheinbaum invita a Omar a integrarse a los trabajos de diseño de -todavía no se puede decir con todas sus letras- la política anticrimen para todo el país. Se entiende que lo querrá de secretario de seguridad, de contrapeso a los generales, de su hombre contra la violencia. \n Qué dirán de esa invitación quienes desde el filomorenismo hoy cuestionan a García Harfuch por sus antecedentes familiares (¡?), su paso por la Policía Federal en tiempos de Peña Nieto (por supuesta carencia de exámenes de confianza, por Ayotzinapa, etc.), y porque no es de izquierda…';
            this.data[1].image = `../../../public/image/logo_esimanal.png`;
            this.logotipo = '../../../public/image/logoipn.png';
        },
        async createNews() {
            const api = 'http://localhost:5000/post-news';
            const response = await axios.post(api, {
                titulo: this.titulo,
                contenido: this.contenido
            });
            
            this.getNews();

            this.titulo = '';
            this.contenido = '';
            this.showCreateNewModal = false;
        },
        async getNews() {
            const api = 'http://localhost:5000/get-news';
            const response = await axios.get(api);
            this.noticias = response.data;
            console.log(this.noticias);
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

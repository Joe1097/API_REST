<!-- Componente para listar las opiniones del usuario -->

<template>

    <div>
        <spinner v-show="loading"></spinner>
        <div v-if="message" class="alert alert-primary" role="alert">{{ message }}</div>
        <div class="mb-4" v-for="opinion in opinions" v-bind:key="opinion.opinion_id">
            <div class="card">
                <h5 class="card-header">{{ opinion.title }}</h5>
                <div class="card-body">
                    <h6 class="card-subtitle mb-2 text-muted">Puntuación: {{ opinion.score }} / 5</h6>
                    <p class="card-text">{{ opinion.resume }}</p>

                    <a class="text-primary" data-toggle="collapse" :href="'#opinion-details-'+opinion.opinion_id" role="button" aria-expanded="false" aria-controls="collapseExample">Ver detalles...</a>

                    <div class="mt-3 collapse" v-bind:id="'opinion-details-'+opinion.opinion_id">
                        <p class="mb-1 card-text font-weight-bold text-secondary">Fecha de envío: <span class="font-weight-normal text-secondary">{{ getDate(opinion.created_at) }}</span></p>
                        <p class="mb-3 card-text font-weight-bold text-secondary">Dirección IP: <span class="font-weight-normal text-secondary">{{ opinion.ip_address }}</span></p>
                        <p class="mb-1 card-text font-weight-bold text-secondary">Usuario: <span class="font-weight-normal text-secondary">{{ opinion.user_name }}</span></p>
                        <p class="mb-3 card-text font-weight-bold text-secondary">email: <span class="font-weight-normal text-secondary">{{ opinion.email }}</span></p>
                        <p class="mb-1 card-text font-weight-bold text-secondary">Empresa: <span class="font-weight-normal text-secondary">{{ opinion.company_name }}</span></p>
                        <p class="mb-0 card-text font-weight-bold text-secondary">Dirección: <span class="font-weight-normal text-secondary">{{ opinion.address }}</span></p>
                    </div>

                </div>
            </div>
        </div>
    </div>    
    
</template>

<script>
    import EventBus from '../event-bus';

    export default {
        data() {    
            return {
                // variable para saber si las opiniones estan cargando y mostrar el spinner
                loading: true,

                // variable para mostrar el mensaje de exito al agregar una opinion
                message: null,

                // arreglo para obtener las opiniones de la BD
                opinions: []
            }
        },
        created(){
            // metodo que se ejecuta al enviar una opinion en el formulario
            EventBus.$on('opinion-added', data => {
                this.opinions.push(data.opinion),
                this.message = data.message
            });
        },
        props:
            // props obtenidos de las variables de la vista opinions/index.blade.php
            ['userId', 'apiToken'],
        mounted(){
            // aqui se obtienen las opiniones del usuario
            axios.get('http://127.0.0.1:8000/api/opinions/'+this.userId+'?api_token='+this.apiToken).then(response => (
                this.opinions = response.data,
                this.loading = false
            ));
        },
        methods:{
            // metodo para mostrar las fechas de las opiniones de manera legible
            getDate(created_at) {
                var date = new Date(created_at);
                var sDate = date.getDate()+" / "+(date.getMonth()+1)+" / "+date.getFullYear();
                return sDate;
            }
        },
    }
</script>
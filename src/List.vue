<template>
    <h2>Esto es la {{ texto }}</h2>
</template>

<script>
    export default {
        name: 'list',
        data () {
            return {
                texto: 'Pagina de contacto'
            }
        }
    }
</script>
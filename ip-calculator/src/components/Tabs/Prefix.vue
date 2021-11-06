<template>
    <div class="container-sub">
        <input-field :title="title"></input-field>
    </div>
</template>
<script>
import InputField from '../InputField.vue'

export default {
    name: "Prefix",
    components : { InputField },
     data() {
        return {
            title: "PREFIJO"
        }
    },
}
</script>
<style scoped>
    .container-sub{
        text-align: center;
    }
</style>
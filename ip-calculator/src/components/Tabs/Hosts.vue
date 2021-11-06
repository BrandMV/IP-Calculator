<template>
    <div class="container-sub">
        <input-field :title="title"></input-field>
    </div>
</template>
<script>
import InputField from '../InputField.vue'

export default {
    name: "Hosts",
    components : { InputField },
     data() {
        return {
            title: "NO. HOSTS"
        }
    },
}
</script>
<style scoped>
    .container-sub{
        text-align: center;
    }
</style>
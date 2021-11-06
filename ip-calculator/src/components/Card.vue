<template>
    <div class="card-container">
        <i :class="icon"></i>
        <div class="card-content">
            <h3>{{ title }}</h3>
            <p>{{ value }}</p>
        </div>
    </div>
</template>
<script>
export default {
    name: "card",
    props: {
        title: String,
        value: String,
        icon: String
    }
}
</script>
<style>
    .card{
        max-width: 100%;
        width: 30.8rem;
        height: 12.3rem;
        background: white;
        border: 1px solid #D3DCD2;
        box-sizing: border-box;
        box-shadow: 0px 4px 4px rgba(0, 0, 0, 0.25);
        border-radius: 10px;
    }
    .card-content{
        display: flex;
        flex-direction: column;
        align-items: center;
    }
    h3{
        font-size: 2.2rem;
        font-weight: 300;
        text-transform: uppercase;
    }
    p{
        font-size: 2.2rem;
        font-weight: bold;
    }
    i{
        font-size: 5rem;
    }
</style>
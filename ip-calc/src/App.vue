<template>
<div class="container">
  <main>
    <h1>calculadora ip</h1>
  </main>

  <section id="tabs-list" class="tabs">
    <button
      v-for="tab in tabs"
      :key = "tab"
      :class="['tab-button', { active: currentTab === tab }]"
      @click = "currentTab = tab"
    >
      {{ tab }}
    </button>
  </section>
  <component :is="currentTabComponent" class="tab container"></component>

</div>
</template>

<script>

import Subnets from './components/Tabs/Subnets.vue'
import Hosts from './components/Tabs/Hosts.vue'
import Prefix from './components/Tabs/Prefix.vue'

export default {
  name: 'App',
  components: { Subnets, Hosts, Prefix },
  el: "#tabs-list",
  data() {
    return {
        currentTab: "Subnets",
        tabs: ["Subnets", "Hosts", "Prefix"],
    }
  },
  computed: {
    currentTabComponent: function() {
      return this.currentTab.toLowerCase()
    }
  }


}
</script>

<style scoped>
.container{
  margin: 0 13.5rem;
  margin-top: 6rem;
  max-width: 100%;
  display: flex;
  align-content: center;
  justify-content: center;
  flex-direction: column;
  max-height: 100vh;
  border: 1px solid red;

}
h1{
  text-transform: uppercase;
  font-size: 5rem;
  text-align: center;
}
main{
  max-width: 100%;
  margin-bottom: 3rem;

}
.tab-button{
  width: 39rem;
  height: 6.9rem;
  background: white;
  border: 2.5px solid #D3DCD2;
  border-radius: 15px 0px 0px 15px;
  transition: .2s all;
  font-size: 2.5rem;
  font-weight: bold;
}
.tab-button:nth-child(2){
  border-radius: 0;
}
.tab-button:nth-child(3){
  border-radius: 0px 15px 15px 0px;
}
.tabs{
  display: flex;
  justify-content: center;
  margin-top: 2rem;
}
.tab-button.active{
  /* animation: color 20ms; */
  animation: color .1s ;
  background: #D3DCD2;
}
.tab-button.active:nth-child(2){
  /* animation: color 20ms; */
  animation: color2 .1s ;
  background: #D3DCD2;
}
.tab-button.active:nth-child(3){
  /* animation: color 20ms; */
  animation: color3 .1s ;
  background: #D3DCD2;
}

.tab{
  margin-top: 6.9rem;
  border: 1px solid red;
}

/* Animaciones */
@keyframes color {
  0%{
    background: linear-gradient(to right, #D3DCD2 0%, white 100%);
  }
  25%{
    background: linear-gradient(to right, #D3DCD2 40%, white 60%);
  }
  50%{
    background: linear-gradient(to right, #D3DCD2 60%, white 40%);
  }
  75%{
    background: linear-gradient(to right, #D3DCD2 80%, white 20%);
  }
  100%{
    background: linear-gradient(to right, #D3DCD2 100%, white 0%);
  }
}
@keyframes color2 {
  0%{
    background: linear-gradient(to bottom, #D3DCD2 0%, white 100%);
  }
  25%{
    background: linear-gradient(to bottom, #D3DCD2 40%, white 60%);
  }
  50%{
    background: linear-gradient(to bottom, #D3DCD2 60%, white 40%);
  }
  75%{
    background: linear-gradient(to bottom, #D3DCD2 80%, white 20%);
  }
  100%{
    background: linear-gradient(to bottom, #D3DCD2 100%, white 0%);
  }
}
@keyframes color3 {
  0%{
    background: linear-gradient(to left, #D3DCD2 0%, white 100%);
  }
  25%{
    background: linear-gradient(to left, #D3DCD2 40%, white 60%);
  }
  50%{
    background: linear-gradient(to left, #D3DCD2 60%, white 40%);
  }
  75%{
    background: linear-gradient(to left, #D3DCD2 80%, white 20%);
  }
  100%{
    background: linear-gradient(to left, #D3DCD2 100%, white 0%);
  }
}
</style>

<template>
    <div class="container-sub">
        <!-- <input-field :title="title"></input-field> -->
        <!-- <input-field :title="title"></input-field> -->
        <!-- <card icon="fas fa-project-diagram" title=""></card> -->
        <form class="form-container">
            <div class="text-field">
                <input v-model.lazy="ip" placeholder="Dirección IP" required @blur="isIpValid()">
                <p>IP {{ ip }}</p>
            </div>
            <div class="text-field">
                <input v-model.lazy="requireSubnets" type="number" :placeholder="title" required @blur="subnetsRestric()" >
            </div>
        </form>
    </div>
</template>
<script>
// import Card from '../Card.vue'

export const hola =  (valid) => {
    console.log(valid);
}
export default {
    name: "Subnets",
    components : { },
    data() {
        return {
            title: "NO. SUBREDES",
            ip: '',
            requireSubnets: 0,
            ipOctects: [],
            completeIP:{}
        }
    },
    methods: {
        /** 
         *  Función utilizada para validar una dirección IP ingresada por el uusario
         *  Hacemos uso de una expresión regular que indica si una IP es válida o no
         *  @returns true si es ip valida o false en caso de no ser una ip valida
        */
        validateIP(){
            //  Testeamos la ip del usuairo con la expresion regular
            if(/^(22[0-3]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/.test(this.ip))
                return true     //  True si es valida
            else return false   //  False si no es valida
        },
        /**
         *  Función que arroja un mensaje si la ip no es valida
         *  @returns Mensaje indicando que no es una ip válida 
        */
        isIpValid(){
            // Si la ip no es válida arrojamos mensaje indicando que ingrese otra IP
            if(!this.validateIP()) 
                alert("Inserte una IP válida o que pertenezca a clase A, B o C")
            else{
                // console.log(this.getOctects(ip));
                this.getOctects()           //  Obtenemos octetos de IP
                this.getIpObject()          //  PObtenemos la IP con sus datos
            }
        },
        /**
         * Función para obtener los octetos de la IP
         * @returns Los octetos de la dirección ip ingresada
        */
        getOctects(){ this.ip.split('.').map(octect => this.ipOctects.push(Number.parseInt(octect,10))) },
        /**
         * Función que inicializa la ip ingresada por el usuario con su clase, mascara, bits para host, la primera subnet, etc.
         * @returns La ip con su información por defecto
        */
        getIpObject() {

            if(this.ipOctects[0] >= 0 && this.ipOctects[0]<= 127){                //  Si la ip es clase A se inicializan los valores
                this.completeIP = {                                           
                    netClass    : "Clase A",                            //  Asignamos la clase
                    defaultMask : "255.0.0.0",                          //  Asignamos la mascara por defecto
                    maskBits    : "11111111000000000000000000000000",   //  Asiganamos los bits de la mascara
                    hostBits    : 24,                                   //  Los bits para host
                    netBits     : 8,                                    //  Los bits para subredes
                    firstSubNet : [this.ipOctects[0], 0, 0, 0]               //  La primera subred de la direccion
                }
            }
            if(this.ipOctects[0] >= 128 && this.ipOctects[0]<= 191){              //  Si la ip es clase B se inicializan los valores
                this.completeIP = {
                    netClass    : "Clase B",                            //  Asignamos la clase
                    defaultMask : "255.255.0.0",                        //  Asignamos la mascara por defecto
                    maskBits    : "11111111111111110000000000000000",   //  Asiganamos los bits de la mascara
                    hostBits    : 16,                                   //  Los bits para host
                    netBits     : 16,                                   //  Los bits para subredes
                    firstSubNet : [this.ipOctects[0], this.ipOctects[1], 0, 0]    //  La primera subred de la direccion
                }
            }
            if(this.ipOctects[0] >= 192 && this.ipOctects[0]<= 223){       //  Si la ip es clase C se inicializan los valores
                this.completeIP = {
                    netClass    : "Clase C",                            //  Asignamos la clase
                    defaultMask : "255.255.255.0",                      //  Asignamos la mascara por defecto
                    maskBits    : "11111111111111111111111100000000",   //  Asiganamos los bits de la mascara
                    hostBits    : 8,                                    //  Los bits para host
                    netBits     : 24,                                   //  Los bits para subredes
                    firstSubNet : [this.ipOctects[0], this.ipOctects[1], this.ipOctects[2], 0] //  La primera subred de la direccion
                }
            }

        },
        //Restricciones

        /**
         * Función que verifica que las sub redes requeridas por el usuario sean acordes al tipo de clase de la red
        */
        subnetsRestric () {
            console.log(this.completeIP.netClass);
            //  Si es clase A y se requieren mas subredes de las que se pueden crear manda error
            if(this.completeIP.netClass == "Clase A" && this.requireSubnets > 4194304)
                alert("Inserte un número valido de subredes, inserte un valor de 1 a 4194304");
            
            //  Si es clase B y se requieren mas subredes de las que se pueden crear manda error
            if(this.completeIP.netClass == "Clase B" && this.requireSubnets > 16384)
                alert("Inserte un número valido de subredes, inserte un valor de 1 a 16384");

            //  Si es clase C y se requieren mas subredes de las que se pueden crear manda error
            if(this.completeIP.netClass == "Clase C" && this.requireSubnets > 64)
                alert("Inserte un número valido de subredes, inserte un valor de 1 a 64");
        }
    }
}
</script>
<style scoped>
    .container-sub{
        /* margin-top: 6.9rem; */
        border: 1px solid blue;
    }
.form-container{
    padding: 0 2rem;
    display: flex;
    justify-content: space-between;
    flex-wrap: wrap;
}
form .text-field{
    position: relative;
    border-bottom: 2px solid #D7816D;
    max-width: 100%;
}
.text-field input{
    max-width: 100%;
    width: 39.2rem;
    /* padding: 0 1rem; */
    height: 5rem;
    font-size: 1.6rem;
    border: none;
    background: none;
    outline: none;
}
</style>
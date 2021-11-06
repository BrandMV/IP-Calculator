<template>
    <div class="container-sub">
        <!-- <input-field :title="title"></input-field> -->
        <!-- <input-field :title="title"></input-field> -->
        <!-- <card icon="fas fa-project-diagram" title=""></card> -->
        <form class="form-container">
            <div class="text-field">
                <input v-model.lazy="ip" placeholder="Dirección IP" required @blur="isIpValid()">
            </div>
            <div class="text-field">
                <input v-model.lazy="requireSubnets" type="number" :placeholder="title" required @blur="ipCalcs()" >
            </div>
        </form>
        <div class="cards" v-if=" cards.length > 1">
            <card
                v-for="(i, index) in 5" :key="index"
                :title="cards[i-1].title"
                :icon="cards[i-1].icon"
                :value="cards[i-1].value"
            >

            </card>
            <button class="calc" @click="subnetsCalc()">calcular</button>
        </div>
        <section class="subnets-list" v-if="hasSubnets">
            <h2>lista de subredes</h2>
            <table>
                <tr>
                    <th>id</th>
                    <th>dirección</th>
                </tr>
                <tbody v-for="(sub, index) in subnetListSubnet" :key="index" > 
                    <tr>
                        <td> {{ index+1 }} </td>
                        <td @click="ale(sub)"> {{ sub.join('.') }} </td>
                    </tr>
               
                </tbody>

            </table>
        </section>
    </div>
</template>
<script>
import Card from '../Card.vue'

export default {
    name: "Subnets",
    components : { Card },
    data() {
        return {
            title: "NO. SUBREDES",
            ip: '',
            res: 0,
            octectToModify : 3,              //Variable para saber el octeto de la ip a modificar, por defecto se modifica el último
            requireSubnets: 0,
            ipOctects: [],
            completeIP:{},
            expoSubnets: 0,
            gotSubnets: 0,
            hostPow: 0,
            totalHostSubnet: 0,
            newMaskSubnet: [],
            prefixSubnet: 0,
            hopSubnet: 0,
            subnetListSubnet: [],
            hostsList: [],
            broadcastAdrrSubnet: [],
            cards: [],
            hasSubnets: false
        }
    },
    methods: {
        /**
         * Función que calcula las direcciones de hosts para una subred especifica
         * @param netClass La clase de la dirección de la IP
         * @param sub La subred elegida
         * @param gotHosts El número de hosts total
         * @returns Retorna las direcciones de host para la subred especifica
        */
        hostCalc (sub){
            let acc = 0                         //  Variable auxiliar de acumulador
            let acc2 = 0                        //  Variable auxiliar de acumulador
            let acc3 = 0                        //  Variable auxiliar de acumulador

            //  Variable temporal que guardara las direcciones de host sin calcular
            let temp = new Array(this.totalHostSubnet).fill(sub)
            
            acc = sub[3]                        //  Inicializamos el ultimo octeto
            acc2 = sub[2]                       //  Inicializamos el tercer octeto
            acc3 = sub[1]                       //  Inicializamos el segundo octeto

            //  Recorremos cada red del arreglo temporal para ahora si calcular las redes de host
            this.hostsList = temp.map((s) => {

                const copy = sub.slice(0)       //  Copiamos la subred
                s = copy                        //  Se almacena la copioa en cada subred

                acc += 1                        //  Vamos incrementando en uno el primer acumulador
                s[3] = acc                      //  El ultimo octeto sera igual al primer acumulador
                
                /*
                    Si el ultimo octeto rebasa los 255 se comienza  a incrementar el acumulador 2 el cual sera
                    lo que incremente el tercer octeto de la red de host. Este acumulador crecerá de uno en uno
                */
                if(s[3] >= 256){
                    acc = 0
                    acc2 += 1
                    s[3] = 0
                    s[2] = acc2
                }
                /*
                    Si el tercer octeto rebasa los 255 se comienza  a incrementar el acumulador 3 el cual sera
                    lo que incremente el segundo octeto de la red de host. Este acumulador crecerá de uno en uno
                */
                if( s[2] >= 256){
                    acc = 0
                    acc2 = 0
                    acc3
                    acc3 += 1
                    s[2] = 0
                    s[1] = acc3
                    
                } 
                s[3] = acc                      //  El acumulador 1 será el ultimo octeto
                s[2] = acc2                     //  El acumulador 2 será el tercer octeto
                s[1] = acc3                     //  El acumulador 3 sera el segundo octeto

                //  Si la clase es tipo A no se cambia el primer octeto
                if(this.completeIP.netClass == "Clase A"){
                    s = s.splice(0)
                    s[0] = sub[0]
                }
                //  Si la clase es tipo B no se cambian los primeros dos octetos
                if(this.completeIP.netClass == "Clase B"){
                    s[0] = sub[0]
                    s[1] = sub[1]
                }
                //  Si la clase es tipo C no se cambian los primeros tres octetos
                if(this.completeIP.netClass == "Clase C"){
                    s[0] = sub[0]
                    s[1] = sub[1]
                    s[2] = sub[2]
                }

                return s                            //  Retornamo cada red de host

            })
        },
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
                this.getIpObject()          //  Obtenemos la IP con sus datos
                this.auxSubmaskCalc()       //  Calculamos variable para ayudarnos a calculos
                this.cards.push({
                    icon:"fas fa-project-diagram", 
                    title: "clase de red", 
                    value: this.completeIP.netClass 
                },
                {
                    icon:"fas fa-mask", 
                    title: "máscara de red", 
                    value: this.completeIP.defaultMask
                }) 
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
        /**
         * Función que calcula una constante que nos servirá para el calculo de las nuevas submascaras de las redes
         * @returns Retorna la cantidad total de los 4 bytes que tiene la red
        */
        auxSubmaskCalc(){
            let resAux = 0;                    //  Inicializamos en 0
            for(let i=0;i<32;i++)               //  Ciclo hasta el número de bits de la red
                resAux = resAux + Math.pow(2,i)   //  Vamos sumando de acuerdo a la potencia de i
            
            this.res = resAux                      //  Retornamos el resultado
        },
        /**
         * Función que calcula la nueva mascara 
         * @param expo El exponente al que esta elevado 
         * @param res  Resultado obtenido de la función auxSubmaskCalc
         * @returns la nueva mascara de la red
        */
        getNewMask(expo){
            /*
                La nueva mascara esta obtenida en una sola línea, se desglozará para mejor entendimiento
                    (res << expo) >>> 0 Primero se realiza un corrimiento de bits a la izquierda de acuerdo al exponente
                                    Posteriormente un ocrrimiento sin signo a la derecha para obtener la cadena de bits completa
                                    de la mascara
                    .toString(2) Pasamos a binario la cantidad obtenida con las operaciones anteriores
                    .match()    Tomamos 8 bits de la cadena de 32 bits
                    .map()      Recorremos de 8 en 8 los bits de la mascara para convertirlos en un entero decimal
            */
            return ( ( ( this.res << expo ) >>> 0).toString(2)).match(/.{1,8}/g).map(byte => parseInt(byte, 2)) 
        },
        /**
         * Función que calcula el prefijo de la nueva submascara
         * @param powSubnet Es la potencia a la que esta elevada la subred
         * @param netBits   Los bits de red de la dirección
         * @returns El prefijo de la nueva submascara de la red
         */
        prefixFunc(){ return this.completeIP.netBits + this.expoSubnets },     //Se suman los bits de red con la potencia de la subred

        /**
         * Función que calcula el salto para ir calculando las subredes de la dirección requerido de acuerdo a los hosts, subredes o prefijo
         * @param {*} prefix El prefijo de la red
         * @param {*} newMask La nueva mascara calculada anteriormente
         * @param {*} netClass La clase de la dirección Ip
         * @returns Retorna el salto para cada subred
        */
        hopFunc(){
            let hop                                 
            if(this.completeIP.netClass == "Clase A")                       //  Si la clase es A:
                if (this.prefixSubnet >= 8 && this.prefixSubnet <= 16){           
                    hop = 256 - this.newMaskSubnet[1]                      //  Si el prefijo esta entre 8 y 16 el salto será de 256 menos el segundo octeto     
                    this.octectToModify = 1                               //  El octeto a modificar sera el segundo
                } else if(this.prefixSubnet >= 17 && this.prefixSubnet <=24){         //  Si el prefijo esta entre 17 y 24:
                    hop = 256 - this.newMaskSubnet[2]                          //  El salto sera 256 - el octeto 3 de la red
                    this.octectToModify = 2                              //  El octeto a modificar sera el 3
                } else hop = 256 - this.newMaskSubnet[3]                   //  En otro caso el salto sera 256 menos el valor del ultimo octeto

            if(this.completeIP.netClass == "Clase B")                       //  Si la clase es B:
                if (this.prefixSubnet >= 16 && this.prefixSubnet <= 24){              //Si el octeto esta entre 16 y 24
                    hop = 256 - this.newMaskSubnet[2]                          //  El salto sera 256 - el octeto 3 de la red
                    this.octectToModify = 2                              //  El octeto a modificar sera el 3
                } else hop = 256 - this.newMaskSubnet[3]               //  En otro caso el salto sera 256 menos el valor del ultimo octeto

            if(this.completeIP.netClass == "Clase C")                       //  Si la clase es C:
                hop = 256 - this.newMaskSubnet[3]                          //  En otro caso el salto sera 256 menos el valor del ultimo octeto       

            return hop                                      //Retornamos el salto resultante
        },
        //Restricciones

        /**
         * Función que verifica que las sub redes requeridas por el usuario sean acordes al tipo de clase de la red
         * @returns Una alerta en caso de solicitar más subredes
        */
        subnetsRestric () {
            console.log(this.completeIP.netClass);
            //  Si es clase A y se requieren mas subredes de las que se pueden crear manda error
            if(this.completeIP.netClass == "Clase A" && this.requireSubnets > 4194304){
                alert("Inserte un número valido de subredes, inserte un valor de 1 a 4194304");
                return false
            } 
                
            //  Si es clase B y se requieren mas subredes de las que se pueden crear manda error
            if(this.completeIP.netClass == "Clase B" && this.requireSubnets > 16384){
                alert("Inserte un número valido de subredes, inserte un valor de 1 a 16384");
                return false
            }
            

            //  Si es clase C y se requieren mas subredes de las que se pueden crear manda error
            if(this.completeIP.netClass == "Clase C" && this.requireSubnets > 64){
                alert("Inserte un número valido de subredes, inserte un valor de 1 a 64");
                return false
             }
            
        },
        /**
         * Función que obtiene los datos como exponentes, nueva mascara, cantidad de hosts y subredes
         * @returns Los calculos hechos con la ip válida
         */
        ipCalcs(){
            if(!this.subnetsRestric()){
                this.expoSubnets = Math.ceil(Math.log2(this.requireSubnets))        //  Calculamos el exponente de la subred
                console.log(this.expoSubnets);
                this.gotSubnets = Math.pow(2,this.expoSubnets)                      //  Calculamos la cantidad de subredes totales            
                this.hostPow = this.completeIP.hostBits - this.expoSubnets               //  Calculamos la potencia necesaria para calcular los hosts                  
                this.totalHostSubnet =  Math.pow(2,this.hostPow)-2                  //  Calculammos el numero total de hosts que tendra cada subred
                this.newMaskSubnet = this.getNewMask(this.hostPow)                   //  Mandamos a llamar a la funcion que calcula nueva mascara
                this.prefixSubnet = this.prefixFunc()                // Calculamos el prefijo
                this.hopSubnet = this.hopFunc()      //  Calculamos el salto de cada subred
                this.cards.push({
                    icon:"fas fa-project-diagram", 
                    title: "subredes", 
                    value: this.gotSubnets
                },
                {
                    icon:"fas fa-users", 
                    title: "Hosts", 
                    value: this.totalHostSubnet
                },
                {
                    icon:"fas fa-theater-masks", 
                    title: "nueva máscara", 
                    value: this.newMaskSubnet.join('.')
                }) 
            }
            
        },

         /**
         * Función que calcula las subredes de la red
         * @param hop El salto de la red
         * @param totalSubnetworks La cantidad total de subredes que tiene la red
         * @param firstSubnet La primera subnet de la red
         * @param netClass La clase de la red
         * @returns La cantidad total de subredes que tiene la red expresada con sus octetos
         */
        subnetsCalc(){
            //En un arreglo temporal vamos a guardar arreglos de subredes que serán modificadas despues
            const temporalNet = new Array(this.gotSubnets).fill(this.completeIP.firstSubNet)
            let acc = 0         //  Variable auxiliar de acumulador
            let acc2 = 0        //  Variable auxiliar de acumulador
            let acc3 = 0        //  Variable auxiliar de acumulador
            this.hasSubnets = true
            //  Por cada subred temporal se calculara la subred real
            this.subnetListSubnet = temporalNet.map( (subnet, index) =>{

                if(index == 0)                      //  Si pasamos por la primera subred la guardamos en el arreglo
                    return subnet                   //  Retornamos la primera subred

                const copy = this.ipOctects.slice(0)     //  Realizamos una copia de los octetos de la red
            
                subnet = copy                       //  Se copian los octetos en cada subred
                acc += this.hopSubnet                          //  Se va acumulando el salto en el primer acumulador
                subnet[this.octectToModify] = acc        //  De acuerdo al octetoa  modificar
                
                if(subnet[this.octectToModify] >= 256){  //  Si el octeto a modificar sobrepasa los 255 se procede a modificar el anterior
                    acc = 0                         //  Empezamos el acumular en 0 de nuevo
                    acc2 += 1                       //  Para calcular las subredes vamos sumando uno al acumulador dos
                    subnet[this.octectToModify] = 0      //  El octeto a modificar se pondrá en 0
                    subnet[this.octectToModify-1] = acc2 //  Ahora si comenzamos con el octeto a anterior a modificarlo
                }
                if(subnet[this.octectToModify-1] >= 256){//  Si el octeto a modificar-1 sobrepasa los 255 se procede a modificar el octeto-2
                    acc = 0                         //  Reiniciamos el primer acumulador
                    acc2 = 0                        //  Reiniciamos el segundo acumulador   
                    acc3 += 1                       //  Procedemos a aumentar el acumulador 3
                    subnet[this.octectToModify-1] = 0    //  Reiniciamos el octetoa modificar-1
                    subnet[this.octectToModify-2] = acc3 //  Comenzamos a modificar el octeto a modificar - 2
                } 
                subnet[this.octectToModify] = acc        //  Finalmente el octeto a modificar será el acumulador
                subnet[this.octectToModify-1] = acc2     //  El octeto-1 será el acumulador 2
                subnet[this.octectToModify-2] = acc3     //  El octeto-2 sera el acumulador 3
                
                if(this.completeIP.netClass == "Clase A"){          //  Si la clase es tipo A
                    subnet = subnet.splice(0)           //  Copiamos la subnet
                    subnet[0] = this.ipOctects[0]            //  El primero octeto de la red será el primer octeto de la red ingresada
                }
                if(this.completeIP.netClass == "Clase B"){          //  Si la clase es tipo B
                    subnet[0] = this.ipOctects[0]            //  El primer octeto de cada subred sera el primero de la red ingresada   
                    subnet[1] = this.ipOctects[1]            //  El segundo octeto de cada subred sera el segundo de la red ingresada
                }
                if(this.completeIP.netClass == "Clase C"){          //  Si la clase es tipo C
                    subnet[0] = this.pOctects[0]            //  El primer octeto de cada subred sera el primero de la red ingresada    
                    subnet[1] = this.ipOctects[1]            //  El segundo octeto de cada subred sera el segundo de la red ingresada
                    subnet[2] = this.ipOctects[2]            //  El tercer octeto de cada subred sera el tercer de la red ingresada
                }
            
                return subnet                       //  Retornamos cada subred
            })      
            // this.broadcastAdrrSubnet = broadcastCalc()
        }
    }
}
</script>
<style scoped>
    .container-sub{
        /* margin-top: 6.9rem; */
     
  
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
.cards{
    margin-top: 5.9rem;
    display: flex;
    flex-wrap: wrap;
    gap: 2.3rem;
    justify-content: center;
}
.calc{
    max-width: 100%;
    width: 30.8rem;
    height: 12.3rem;
    background: #1E1E2A;
    border: 1px solid #D3DCD2;
    box-sizing: border-box;
    box-shadow: 0px 4px 4px rgba(0, 0, 0, 0.25);
    border-radius: 10px;
    font-size: 3rem;
    color: white;
    text-transform: uppercase;
    font-weight: bold;
    transition: 0.2s ease-in-out;
    cursor: pointer;
}
button:hover{
    transform: translateY(-3%);
}
h2{
    text-transform: uppercase;
    font-size: 2.5rem;
    font-weight: bold;
}
table {
  border-collapse: collapse;
  width: 100%;
  text-align: center;
  font-size: 1.8rem;
}

th{
    font-weight: bold;
    text-transform: uppercase;
}
td,th{
    border: 1px solid #C0C0C0;
    padding: 1rem;
}
tr:nth-child(even) {
  background-color: #dddddd;
}
.subnets-list{
    border: 1px solid green;
    margin-top: 5rem;
    overflow: auto;
    max-height: 100%;
}

</style>
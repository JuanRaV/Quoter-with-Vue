<script setup> //Escribimos todos los imports o codigo javascript de dicho componente COMPOSITION, OPTIONS es mas codigo y dificil
  import{ref,computed,watch} from 'vue'
  import Header from './components/Header.vue'
  import Button from './components/Button.vue'
  import {calcularTotalPagar} from './helpers'
  //Definiendo el State tanto con ref como con reactive
  const cantidad = ref(10000);
  const meses = ref(6);
  const total = ref(0);

  const MIN = 0;
  const MAX = 20000;
  const STEP = 100;

  const formatearDinero = (valor)=>{
    const formatter = new Intl.NumberFormat('en-US',{
      style:'currency',
      currency:'USD'
    });

    return formatter.format(valor);
  }
  watch([cantidad,meses],()=>{ //Registra el cambio para que se actualicen los datos (Como un useEffect en React)
    total.value = calcularTotalPagar(cantidad.value,meses.value)
  });

  const pagoMensual = computed(()=>{
    return total.value/meses.value;
  });

  const handleChangeDecrement= ()=>{
    const valor = cantidad.value-STEP
    if(valor < MIN){
      alert('Cantidad no valida')
      return;
    }
    cantidad.value = valor
  }

  const handleChangeIncrement= ()=>{
    const valor = cantidad.value+STEP
    if(valor > MAX){
      alert('Cantidad no valida')
      return;
    }
    cantidad.value = valor
  }
</script>

<template> <!--Se escribe el html del dicho componente-->
    <div class="my-20 max-w-lg mx-auto bg-white shadow p-10">
      <Header/>
      <div class="flex justify-between mt-10">
        <Button
          :operador = "'-'" 
          @fn = "handleChangeDecrement"
        />
        <Button
          :operador = "'+'"
          @fn = "handleChangeIncrement"
        />

      </div>
      <div class="my-5">
        <input 
          type="range"
          class="w-full bg-gra-200 accent-lime-500 hover:accent-lime-600"
          :min = "MIN"
          :max = "MAX"
          :step= "STEP"
          v-model.number = "cantidad"
        />
        <p class="text-center my-10 text-5xl font-extrabold text-indigo-600">{{formatearDinero(cantidad)}}</p>

        <h2 v-if="total>0" class="text-2xl font-extrabold text-gray-500 text-center">
          Elige un <span class="text-indigo-600">Plazo</span> a pagar
        </h2>

        <select 
          class="w-full p-2 bg-white border border-gray-300 rounded-lg text-center text-xl font-bold text-gray-500 mt-5"
          :value="meses"
          v-model.number = "meses"
        >
          <option value="6">6 meses</option>
          <option value="12">12 meses</option>
          <option value="24">24 meses</option>
        </select>
      </div>

      <div v-if="total>0" class="my-5 space-y-3 bg-gray-50 p-5">
        <h2 class="text-2xl font-extrabold text-gray-500 text-center">
          Resumen <span class="text-indigo-600">de pagos</span>
        </h2>

        <p class="text-xl text-gray-500 text-center font-bold">{{ meses }} Meses</p>
        <p class="text-xl text-gray-500 text-center font-bold">Total a pagar: {{formatearDinero(total)}}</p>
        <p class="text-xl text-gray-500 text-center font-bold">Mensuales: {{formatearDinero(pagoMensual)}}</p>
      </div>

      <p v-else class="text-2xl font-extrabold text-gray-500 text-center">Elige un <span class="text-indigo-600">plazo</span> y una <span class="text-indigo-600">cantidad</span> para <span class="text-indigo-600">comenzar</span></p>
    </div>
</template>



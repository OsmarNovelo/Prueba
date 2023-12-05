<template>
	<div class="text-center">
	
		<div class="flex items-center justify-center space-x-4">
		
			<div class="mt-6">
				<h1 class="text-3xl font-bold">SITMOTUL</h1>
				<br>
				<h2 class="mb-2 text-xl">
					Estado de la evaluación tutor del periodo {{ periodo }}
					al {{ obtenerFechaActual() }}
				</h2>
			
			</div>
		</div>

	
		<div class="flex justify-center space-x-10 mt-6">
			
			<div class="w-3/2 p-3 rounded-lg">
				<div v-for="(data, index) in infoGeneral" :key="index"
					class="bg-green-500 h-full p-4 shadow-md rounded-md mb-5 flex items-center justify-center">
					
					<div class="text-center">
						<p class="mb-2 px-3 text-cyan-50 font-bold text-3xl">
							Dias restantes: {{ calcularHorasRestantes(data.fin).dias }}d 
						</p>

						<p class="mb-2 px-3 text-center text-black font-semibold">para finalizar</p>
						<p class="mb-2 px-3 text-cyan-50 font-bold text-3xl">
							Horas restantes: {{ calcularHorasRestantes(data.fin).horas }}h 
						</p>
						<p class="mb-2 px-3 text-center text-black font-semibold">para cerrar</p>
					</div>
				</div>
			</div>

			
			<div class="w-2/6 grid grid-cols-1 gap-2">

				<div v-for="(data, index) in infoGeneral" :key="index"
					class=" p-3 rounded-lg mb-5">
					<div class="bg-green-500 p-4 shadow-md rounded-md">
						<p class="b-2 px-3 text-cyan-50 font-bold text-3xl">
							{{ calcularPorcentajeFaltante(data.alEvaluados, data.alTotal) }} % 
						</p>
						<p class="font-semibold">
							Alumnos faltantes por evaluar: {{ data.alTotal - data.alEvaluados }} de {{ data.alTotal }}
						</p>
					</div>
				</div>



				<div v-for="(data, index) in infoGeneral" :key="index"
					class=" p-3 rounded-lg mb-5">
					<div class="bg-green-500 p-4 shadow-md rounded-md">
						<p class="mb-2 px-3 text-cyan-50 font-bold text-3xl">
							{{ calcularPorcentaje(data.alEvaluados, data.alTotal) }} % 
						</p>
						<p class="font-semibold">Hay {{ totalEvaluados }} alumnos que han resuelto la evaluación de un total de {{
							totalAlumnos }} alumnos</p>


					</div>
				</div>

							
			</div>
		</div>

		
<div class="mt-6">
	<div v-if="infoGeneral.length > 0 && infoPersonal.length > 0" class="m-auto w-11/12 ">
   

    <div class="grid grid-cols-5 gap-5 mx-3">
      <div v-for="(data, index) in infoPersonal" :key="index" class="relative mb-10">
        <div class="rounded-full bg-white h-16 w-16 flex items-center justify-center mx-auto mb-4">
          <h1 class="text-xl font-bold text-black">IEM</h1>
        </div>
        <div v-if="data.IEM" class="bg-white p-4">
          <p>Listas: {{ data.IEM.listas }}, Faltantes: {{ data.IEM.faltantes }}</p>
        </div>
      </div>

      <div v-for="(data, index) in infoPersonal" :key="index" class="relative">
        <div class="rounded-full bg-white h-16 w-16 flex items-center justify-center mx-auto mb-4">
          <h1 class="text-xl font-bold text-black">IER</h1>
        </div>
        <div v-if="data.IER" class="bg-white p-4">
          <p>Listas: {{ data.IER.listas }}, Faltantes: {{ data.IER.faltantes }}</p>
        </div>
      </div>

      <div v-for="(data, index) in infoPersonal" :key="index" class="relative">
        <div class="rounded-full bg-white h-16 w-16 flex items-center justify-center mx-auto mb-4">
          <h1 class="text-xl font-bold text-black">ISC</h1>
        </div>
        <div v-if="data.ISC" class="bg-white p-4">
          <p>Listas: {{ data.ISC.listas }}, Faltantes: {{ data.ISC.faltantes }}</p>
        </div>
      </div>

      <div v-for="(data, index) in infoPersonal" :key="index" class="relative">
        <div class="rounded-full bg-white h-16 w-16 flex items-center justify-center mx-auto mb-4">
          <h1 class="text-xl font-bold text-black">II</h1>
        </div>
        <div v-if="data.II" class="bg-white p-4">
          <p>Listas: {{ data.II.listas }}, Faltantes: {{ data.II.faltantes }}</p>
        </div>
      </div>

	  <div v-for="(data, index) in infoPersonal" :key="index" class="relative">
        <div class="rounded-full bg-white h-16 w-16 flex items-center justify-center mx-auto mb-4">
          <h1 class="text-xl font-bold text-black">IER</h1>
        </div>
        <div v-if="data.IER" class="bg-white p-4">
          <p>Listas: {{ data.IER.listas }}, Faltantes: {{ data.IER.faltantes }}</p>
        </div>
      </div>
    </div>
  </div>
  <div v-else>
    <p>No hay datos de alumnos evaluados disponibles.</p>
  </div>
</div>
	</div>
</template>

<script setup>
import { onMounted } from 'vue'
import { ref } from 'vue'

// Definir variables reactivas
const infoGeneral = ref([]) 
const infoPersonal = ref([])


const totalEvaluados = ref(0)
const totalAlumnos = ref(0)
const periodo = ref('')

// Función para obtener datos de la API
async function fetchData(apiUrl, infoArray) {
	try {
		const response = await fetch(apiUrl)
		const data = await response.json()
		infoArray.value.push(data) 

		
		totalEvaluados.value += data.alEvaluados || 0
		totalAlumnos.value += data.alTotal || 0
		if (!periodo.value) {
			periodo.value = data.periodo || ''
		}

		console.log('Datos recibidos:', data)
		
		if (data.ObjectIE) {
			console.log('ObjectIE:', data.ObjectIE)
		}
		if (data.IEM) {
			console.log('IEM:', data.IEM)
		}
		if (data.IER) {
			console.log('IER:', data.IER)
		}
		if (data.II) {
			console.log('II:', data.II)
		}
		if (data.ISC) {
			console.log('ISC:', data.ISC)
		}
		if (data.IRL) {
			console.log('IRL:', data.IRL)
		}

	} catch (error) {
		console.error('Error al obtener datos:', error)
	}
}


onMounted(async () => {
	
	await fetchData('https://sitmotul.com.mx/api/statusEval', infoGeneral)

	
	await fetchData('https://sitmotul.com.mx/api/statusEvalIng', infoPersonal)
})


const calcularHorasRestantes = (fechaFin) => {
	const ahora = new Date();
	const fechaFinalizacion = new Date(fechaFin);
	const diferenciaTiempo = fechaFinalizacion - ahora;

	const diferenciaDias = Math.floor(diferenciaTiempo / (1000 * 60 * 60 * 24));
	const diferenciaHoras = Math.floor(diferenciaTiempo / (1000 * 60 * 60));

	return {
		dias: diferenciaDias >= 0 ? diferenciaDias : 0,
		horas: diferenciaHoras >= 0 ? diferenciaHoras : 0,
	};
};


const calcularPorcentaje = (evaluados, total) => {
	if (total === 0) {
		return 0;
	}
	return ((evaluados / total) * 100).toFixed(2);
};


const calcularPorcentajeFaltante = (evaluados, total) => {
	const faltantes = total - evaluados;
	if (total === 0 || faltantes <= 0) {
		return 0;
	}
	return ((faltantes / total) * 100).toFixed(2);
};




const obtenerFechaActual = () => {
	const opcionesFecha = {
		weekday: "long",
		day: "numeric",
		month: "long",
		year: "numeric",
	};
	const fechaActual = new Date();
	return fechaActual.toLocaleDateString("es-MX", opcionesFecha);
};
</script>
		

  
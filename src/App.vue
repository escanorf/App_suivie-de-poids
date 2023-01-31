<script setup>
import{ ref , shallowRef , computed , watch ,nextTick} from 'vue'
import Chart from 'chart.js/auto'

const poids = ref([])

const poidsChartEl = ref(null)

const poidsChart = shallowRef(null)

const poidsInput = ref(60.0)

const currentPoids = computed (() =>{
  return poids.value.sort((a,b) => b.date - a.date)[0] ||  {poids:0}
})

const  addpoids = () => {
  poids.value.push({
    poids: poidsInput.value,
    date: new Date().getTime()
  })
}

watch(poids, newPoids =>{
  const pds = [...newPoids]

    if (poidsChart.value){
      poidsChart.value.data.labels = pds
         .sort((a, b) => a.date - b.date)
         .map(p => new Date(p.date).toDateString())
         .slice (-7)


         poidsChart.value.data.datasets[0].data = pds
         .sort((a, b) => a.date - b.date)
         .map(p => p.poids)
         .slice (-7)

         poidsChart.value.update()

         return
    }




  nextTick(() => {

    poidsChart.value = new Chart (poidsChartEl.value.getContext('2d'),{
      type: 'line',
      data:{
        labels : pds
        .sort((a, b) => a.date - b.date)
        .map(p => new Date(p.date).toDateString()),

        datasets:[
            {
              label:"poids",
              data : pds
              .sort((a, b) => a.date - b.date)
              .map(p => p.poids)
              ,backgroundColor :'rgba(245, 40, 145, 0.44)',
                borderColor:'rgb (255, 105, 180)', 
                borderWidth: 1 ,
                fill:true,
            }
        ]
      },
      options:{
        responsive : true ,
        maintainAspectRatio: false,
      }
    })
  })

  
}, {deep:true})


</script>

<template>
  <main>
    
    <h1>Suivi De Poids</h1>
      
          <div class="current flexbox">
            <span> {{currentPoids.poids }} </span>
            <small>  KG</small>
            </div>
      
        <form @submit.prevent="addpoids">
          <input type="number" step="0.1" v-model="poidsInput" />
          <input type="submit" value="Ajouter" /> 
          </form>

            <div v-if="poids && poids.length > 0">
              <h2>Les 7 derniers Jours</h2>
              <div class="canvas-box">
                <canvas ref="poidsChartEl"></canvas>
                </div>

                <div class="historique-poids">
                  <h2>historique poids</h2>
                  <ul>
                    <li v-for="poids in poids">
                      <span> {{ poids.poids }} Kg  </span>
                      <small> {{ new Date(poids.date).toLocaleDateString()  }}  </small>
                      </li>
                    </ul>
                  </div>
              </div>    
    </main>
</template>

<style>

*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family:Arial, Helvetica, sans-serif;

}



body {
 
  background-color: grey;
}
main{
  padding: 1.5rem;
}
  h1{
    font-size: 2em;
    text-align: center;
    margin-bottom: 2rem;
  }
H2{
  margin-bottom: 1rem;
  color: #eee;
  font-weight: 400;
}

.flexbox{
  margin: auto;
}
.current{
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;


  width: 200px;
  height: 200px;

   text-align: center;
   background-color: salmon;
   border-radius: 999px;
   box-shadow: 0 4px 12px rgba(0,0,0, 0,1);
   border: 5px solid salmon;

   margin: 0 auto 2 rem;
}
.current span {
  display: block;
  font-size: 2em;
  font-weight: bold;
  margin-bottom: 0.5rem;
  color: black;
}
.current small{
  color: black;
  font-size: italic;
}
form{
  display: flex;
  margin-bottom: 2rem;
  border: 1px solid #AAA;
  border-radius: 0.5rem;
  overflow:hidden;
  transition: 200ms linear;
}
form:focus-within,
form:hover{
  border-color:salmon ;
  border-width: 2px;
}

form input[type="number"]{
appearance: none;
outline: none;
border: none;
background-color: grey;
text-align: center;
flex: 1 1 0%;
width: 40px;
padding: 1 rem 1.5rem;
font-size: 1.25rem;
}

form input[type="submit"]{
  appearance: none;
  outline: none;
  border: none;
  cursor: pointer;
  background-color: salmon;

  padding: 0.5rem 1rem;

  color: white;
  font-size: 1.25rem;
  font-weight: 700;
  transition: 200ms linear;
  border-left: 3px solid transparent;
}


form input[type="submit"]:hover{
  background-color: white;
  color: salmon;
  border-left-color: salmon;
}

.canvas-box{
  width: 100%;
  max-width: 720px;
  background-color: salmon;
  padding: 1rem;
  border-radius: 0.5rem;
  box-shadow: 0 4px 12px rgba(250, 101, 0, 0.34);
  margin-bottom: 2rem;
}

.historique-poids ul{
  list-style: none;
  padding: 0;
  margin: 0;
}
.historique-poids ul li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.5rem;
  cursor: pointer;

}
.historique-poids ul:nth-child(even){
  background-color: #dfdfdfdf;

}
.historique-poids ul li :hover{
  background-color: #f8f8f8;
}
.historique-poids ul li :last-of-type{
  border-bottom: none;
}

.historique-poids ul li span {
display: block;
font-size: 1.25rem;
font-weight: 700;
margin-right: 1rem;
}

.historique-poids ul li small {
  color: salmon;
  font-size: italic;
}

</style>

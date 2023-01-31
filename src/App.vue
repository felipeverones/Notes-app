<script setup>

import { ref, onMounted, watch, computed } from 'vue'

const showModal = ref(false)

const newNote = ref("")

const notes = ref([])

const errorMessage = ref("")


function getRandomColor() {
  let color = "hsl(" + Math.random() * 360 + ", 100%, 75%)";
  return color;
}


const addNote = () =>{
  if(newNote.value.length <= 9){
    return errorMessage.value = "Note needs to be 10 characters or more!"
  }
  notes.value.push({
    createdAt: new Date().getTime(),
    text: newNote.value,
    date: new Date().toLocaleDateString("pt-BR"),
    backgroundColor: getRandomColor(),
  })

  newNote.value = ''
  showModal.value = false
  errorMessage.value = ""
}

const removeNote = note =>{
  notes.value = notes.value.filter(n => n !== note)
}

watch(notes, newVal =>{
  localStorage.setItem('notes', JSON.stringify(newVal))
}, {deep: true})


onMounted(()=>{
  notes.value = JSON.parse(localStorage.getItem('notes')) || []
})

const notes_asc = computed( () => notes.value.sort((a,b) =>{
  return b.createdAt - a.createdAt
}))

</script>


<template>
  <main>

    <div v-if="showModal" class="overlay">
      <div class="modal">
        <textarea v-model.trim="newNote" name="note" id="note" cols="30" rows="10"></textarea>
        <p class="errormessage" v-if="errorMessage">{{ errorMessage }}</p>
        <button @click="addNote">Add Note</button>
        <button class="close" @click="showModal = false; newNote =`` ">Close</button>
      </div>
    </div>

    <div class="container">
      <header>
        <h1>Notes</h1>
        <button @click="showModal = true">+</button>
      </header>
      <div class="cards-container">

        <p class="no-notes" v-if="!notes.length">No notes yet</p>

        <div v-for="note in notes_asc" :key="note.createdAt" class="card" :style="{backgroundColor: note.backgroundColor}">
          <p class="main-text">{{note.text}}</p>
          <p class="date">{{note.date}}</p>
          <button @click="removeNote(note)">Delete</button>

        </div>
        
      </div>
    </div>
  </main>
</template>

<style scoped>
  main{
    max-height: 100vh;
    max-width: 100vw; 
  }

  .container{
    max-width: 1000px;
    padding: 10px;
    margin: 0 auto;
  }

  header{
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 25px;
  }

  h1{
    font-weight: bold;
    
    font-size: 75px;
  }

  header button{
    border: none;
    padding: 10px;
    width: 50px;
    height: 50px;
    cursor: pointer;
    background-color: rgb( 21,20,20);
    border-radius: 100%;
    color:white;
    ;
  }

  .card{
    width: 225px;
    height: 225px;
    background-color: rgb(237,258,44);
    padding: 10px;
    border-radius: 15px;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    margin-right: 20px;
    margin-bottom: 20px;
  }

  .date{
    font-size: 12.5px;
    font-weight: bold;
  }

  .cards-container{
    display: flex;
    flex-wrap: wrap;
  }

  .overlay{
    position: absolute;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.77);
    z-index: 10;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .modal{
    width: 750px;
    background-color: white;
    border-radius: 10px;
    padding: 30px;
    position: relative;
    display: flex;
    flex-direction: column;
  }

  .modal button{
    padding: 10px 20px;
    font-size: 20px;
    width: 100%;
    background-color: blueviolet;
    border: none;
    color: white;
    cursor: pointer;
    margin-top: 15px;
    border-radius: 15px;
  }

  textarea{
    border-radius: 15px;
    padding: 8px;
  }

  .modal .close{
    background-color: red;
    margin-top: 7px;
  }

  .no-notes{
    margin-top: 40px;
  }

  .errormessage{
    color: red;
    font-weight: bold;
    margin-top: 15px;
    align-self: center;
  }

</style>
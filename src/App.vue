<script setup>
  import axios from 'axios' //  axios = 'gem' à utiliser pour fetch une API
  import { ref, onMounted, watch } from "vue" // ref car reactivity-core (voir plus bas) ; onMounted = au moment où l'app se connecte ; watch = addEventListener

  const users = ref([]) // https://vuejs.org/api/reactivity-core.html#ref array vide
  const userName = ref('') // https://vuejs.org/api/reactivity-core.html#ref string vide
  const userPicture = ref('') // https://vuejs.org/api/reactivity-core.html#ref string vide
  const selectedUser = ref({}) // https://vuejs.org/api/reactivity-core.html#ref object vide

  onMounted(async () => { // async = syntaxe pour AJAX
  users.value = (await axios.get('https://randomuser.me/api/?results=5&nat=fr')).data.results // results = limite au nombre de résultats ; nat = nationalité des users
  selectedUser.value = users.value[0] // par défaut, le user selectedUser est celui à l'indice 0 de l'array users
  })

  watch(selectedUser, (updatedUser) => { // +/- addEventListener sur v-model="selectedUser"
    userName.value = `${updatedUser.name.first.toUpperCase()} ${Array.from(updatedUser.name.last.toUpperCase())[0]}.` // changer le nom affiché par celui sélectionné dans la liste ; seulement initiale du nom
  })

  function updateUserPicture() { // déclaration d'une fonction pour utiliser dans un callback
    userPicture.value = selectedUser.value.picture.large // l'url de la photo est celle du user sélectionné, taille large
  }
</script>

<template>
  <main>
    <div class="wrapper">
      <div class="left">
        <h1>Bonjour</h1>
        <h1>{{ userName }}</h1>

        <p>Séléctionner un nouveau profil à l'aide de cette liste :
              <select v-model="selectedUser"> <!-- déclencheur pour le watch ; select = balise pour liste déroulante -->
                <option v-for="(user, index) in users" :key="index" :value="user">{{ user.name.first }} {{ user.name.last }}</option> <!-- dans l'array de users on créé la liste déroulante ; :key pour que chaque user est une key unique ; value pour que chaque option ait le nom du user -->
              </select>
        </p>
        <button @click.prevent="updateUserPicture" id="btn-profile">AFFICHER SA PHOTO DE PROFIL</button> <!-- à chaque click, on appelle la fonction updateUserPicture ; prevent pour empêcher le rechargement de la page -->
      </div>

      <div class="right">
        <div class="back">
          <img src="./assets/img/back-arrow.svg" alt="Flèche de fond" id="back-arrow">
        </div>
        <div class="picture-arrow">
          <div class="middle">
            <img :src="userPicture || 'src/assets/img/hero-image.jpg'" alt="Photo de profil" id="profile">
          </div>
          <div class="front">
            <img src="./assets/img/front-arrow.svg" alt="Flèche de devant" id="front-arrow">
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<style>
  .wrapper{
    display: flex;
    height: 100vh;
    align-items: center;
  }
  .left{
    z-index: 5000;
    padding-left: 20px;
  }
  .right{
    display: flex;
    align-items: center;
  }
  #back-arrow{
    z-index: -1000;
  }
  #front-arrow{
    height: 640px;
    z-index: 1000;
  }
  #profile{
    border-radius: 50%;
    box-shadow: rgba(17, 12, 46, 0.15) 0px 48px 100px 0px;
    width: 550px;
  }
  .picture-arrow{
    margin-right: calc(30vw - 100px);
  }
  #btn-profile{
    background-color: black;
    border-radius: 100px;
    border: none;
    padding: 15px;
    color: white;
  }
  .back, .middle, .front{
    position: absolute;
    right: 0;
  }
  .middle, .front{
    top: 200px;
  }
  .middle{
    right: 130px;
  }
  h1, p{
    font-family: 'Noto Serif', serif;
    color: rgb(82, 82, 82);
  }
</style>

<script setup>
import { onMounted, ref } from 'vue';
import { RouterLink, useRoute } from 'vue-router';
const route = useRoute();
const API_URL = 'http://localhost:3000';
const tutor = ref(null);
const pet = ref(null);

async function carregarPet() {
  const idPet = route.params.id;

  try {
    const respostaPets = await fetch(`${API_URL}/pets/${idPet}`);
    pet.value = await respostaPets.json();

    if (pet.value?.tutorId) {
      const respostaTutor = await fetch(`${API_URL}/tutores/${pet.value.tutorId}`);
      tutor.value = await respostaTutor.json();
    }
  } catch (error) {
    console.error('Erro ao carregar pet:', error);
  }
}

function DeletarPet () {

}


onMounted(carregarPet);
</script>

<template>
  <div v-if="pet">
    <h1>Nome do Pet: {{ pet.nome }}</h1>
    <p>Espécie: {{ pet.especie }}</p>
    <p>Nome do Tutor: {{ tutor?.nome || 'Tutor não encontrado' }}</p>
    <button class="btn btn-default"> Deletar</button>
    <RouterLink class="btn btn-secondary" :to="{ name: 'pets' }">
      Voltar
    </RouterLink>
    <RouterLink  class="btn btn-secondary" > Atualizar</RouterLink>
  </div>

  <p v-else>Carregando pet...</p>
</template>

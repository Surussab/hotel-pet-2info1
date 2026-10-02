<script setup>
import { onMounted, ref } from 'vue';
import { RouterLink, useRouter } from 'vue-router';

const novoPet = ref({
  nome: '',
  especie: '',
  tutorId: '',
});

const router = useRouter();
const API_URL = 'http://localhost:3000';

const tutores = ref([]);
const carregandoTutores = ref(true);
const erro = ref('');

async function carregarTutores() {
  try {
    const resposta = await fetch(`${API_URL}/tutores`);

    if (!resposta.ok) {
      throw new Error('Erro ao carregar tutores');
    }

    tutores.value = await resposta.json();
  } catch (error) {
    erro.value = 'Não foi possível carregar os tutores.';
    console.error(error);
  } finally {
    carregandoTutores.value = false;
  }
}

async function salvarPet() {
  try {
    const resposta = await fetch(`${API_URL}/pets`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(novoPet.value),
    });

    if (!resposta.ok) {
      throw new Error('Erro ao cadastrar pet');
    }

    router.push({ name: 'pets' });
  } catch (error) {
    erro.value = 'Não foi possível cadastrar o pet.';
    console.error(error);
  }
}

onMounted(carregarTutores);
</script>

<template>
  <div>
    <header class="mb-4">
      <h1 class="text-2xl font-bold">Cadastro de Pet</h1>
      <p class="text-body-secondary mb-0">Cadastro de Pets no sistema.</p>
    </header>

    <p
      v-if="erro"
      class="alert alert-danger"
      role="alert"
    >
      {{ erro }}
    </p>

    <p v-if="carregandoTutores">Carregando tutores...</p>

    <form
      v-else
      @submit.prevent="salvarPet"
      class="row g-3"
    >
      <div class="col-md-6">
        <label
          for="nome"
          class="form-label"
        >
          Nome do Pet
        </label>

        <input
          type="text"
          id="nome"
          v-model="novoPet.nome"
          class="form-control"
          required
        />
      </div>

      <div class="col-md-6">
        <label
          for="especie"
          class="form-label"
        >
          Espécie
        </label>

        <select
          id="especie"
          v-model="novoPet.especie"
          class="form-select"
          required
        >
          <option
            value=""
            disabled
          >
            Selecione a Espécie
          </option>
          <option value="Cachorro">Cachorro</option>
          <option value="Gato">Gato</option>
        </select>
      </div>

      <div class="col-md-6">
        <label
          for="tutor"
          class="form-label"
        >
          Tutor
        </label>

        <select
          id="tutor"
          v-model="novoPet.tutorId"
          class="form-select"
          required
        >
          <option
            value=""
            disabled
          >
            Selecione um Tutor
          </option>
          <option
            v-for="tutor in tutores"
            :key="tutor.id"
            :value="tutor.id"
          >
            {{ tutor.nome }}
          </option>
        </select>
      </div>

      <div class="col-12 mt-3">
        <button
          type="submit"
          class="btn btn-primary me-2"
        >
          Salvar Pet
        </button>

        <RouterLink
          class="btn btn-secondary"
          :to="{ name: 'pets' }"
        >
          Voltar
        </RouterLink>
      </div>
    </form>
  </div>
</template>

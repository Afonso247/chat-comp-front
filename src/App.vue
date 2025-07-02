<template>
  <div class="container">
    <h1>Comparador de Respostas</h1>

    <div class="input-section">
      <textarea v-model="prompt" placeholder="Digite seu prompt aqui..."></textarea>
      <button @click="getResponses" :disabled="loading">Enviar</button>
    </div>

    <div v-if="loading" class="loading">Gerando respostas...</div>

    <div v-else-if="responses.length" class="responses">
      <div
        v-for="(response, index) in responses"
        :key="index"
        class="response-card"
        @click="vote(response.type)"
      >
        <h3>Resposta {{ index + 1 }}</h3>
        <div class="response-content">
          <p>{{ response.content }}</p>
        </div>
        <div class="response-label">
          {{ response.type === 'ana' ? 'Ana (TCC)' : 'ChatGPT Padrão' }}
        </div>
      </div>
    </div>

    <div v-if="voted" class="thank-you">
      Obrigado pelo seu voto! ✅
      <button @click="reset">Nova comparação</button>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      prompt: '',
      responses: [],
      loading: false,
      voted: false,
    }
  },
  methods: {
    async getResponses() {
      if (!this.prompt.trim()) return

      this.loading = true
      try {
        const response = await axios.post('http://localhost:4000/api/responses', {
          prompt: this.prompt,
        })
        this.responses = response.data.responses
      } catch (error) {
        console.error(error)
        alert('Erro ao gerar respostas')
      } finally {
        this.loading = false
      }
    },
    async vote(responseType) {
      try {
        await axios.post('http://localhost:4000/api/votes', {
          prompt: this.prompt,
          chosenResponseType: responseType,
        })
        this.voted = true
      } catch (error) {
        console.error(error)
        alert('Erro ao registrar voto')
      }
    },
    reset() {
      this.prompt = ''
      this.responses = []
      this.voted = false
    },
  },
}
</script>

<style>
.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 2rem;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

h1 {
  text-align: center;
  color: #2c3e50;
  margin-bottom: 2rem;
}

.input-section {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  margin-bottom: 2rem;
}

textarea {
  width: 100%;
  height: 120px;
  padding: 1rem;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 1rem;
  resize: vertical;
}

button {
  padding: 0.8rem 1.5rem;
  background: #3498db;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 1rem;
  transition: background 0.3s;
}

button:hover {
  background: #2980b9;
}

button:disabled {
  background: #bdc3c7;
  cursor: not-allowed;
}

.loading {
  text-align: center;
  padding: 2rem;
  font-size: 1.2rem;
  color: #7f8c8d;
}

.responses {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1.5rem;
}

.response-card {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 1.5rem;
  cursor: pointer;
  transition: all 0.3s;
  position: relative;
  display: flex;
  flex-direction: column;
  height: 100%;
}

.response-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
  border-color: #3498db;
}

.response-content {
  flex-grow: 1;
  overflow-y: auto;
  max-height: 300px;
  margin-bottom: 1rem;
}

.response-label {
  padding: 0.5rem;
  background: #f8f9fa;
  border-radius: 4px;
  text-align: center;
  font-weight: bold;
  color: #2c3e50;
  margin-top: auto;
}

.response-card:nth-child(1) .response-label {
  background: #e3f2fd;
  color: #1976d2;
}

.response-card:nth-child(2) .response-label {
  background: #e8f5e9;
  color: #388e3c;
}

.thank-you {
  text-align: center;
  padding: 2rem;
  font-size: 1.5rem;
  color: #27ae60;
}
</style>

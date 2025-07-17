<template>
  <!-- Consent Screen -->
  <div class="consent-container" v-if="!consentGiven">
    <div class="consent-content">
      <h2>📝 TERMO DE CONSENTIMENTO LIVRE E ESCLARECIDO</h2>
      <p v-if="!showConsentText" @click="toggleConsentText" class="toggle-consent">
        Abrir o termo <span>▼</span>
      </p>
      <p v-else @click="toggleConsentText" class="toggle-consent">Fechar o termo <span>▲</span></p>
      <div v-show="showConsentText" class="consent-dropdown">
        <div class="consent-text">
          <p>
            O(A) Senhor(a) está sendo convidado(a) a participar de uma pesquisa. Por favor, leia
            este documento com bastante atenção antes de assiná-lo. Caso haja alguma palavra ou
            frase que o(a) senhor(a) não consiga entender, converse com o pesquisador responsável
            pelo estudo ou com um membro da equipe desta pesquisa para esclarecê-los.
          </p>
          <p>
            A proposta deste termo de consentimento livre e esclarecido (TCLE) é explicar tudo sobre
            o estudo e solicitar a sua permissão para participar do mesmo.
          </p>
          <p>
            O objetivo desta pesquisa é conduzir um estudo comparativo para avaliar a percepção de
            utilidade das respostas fornecidas por dois modelos de assistentes virtuais, com a
            função de providenciar assistência terapêutica quando expostos à demandas de apoio
            emocional e aconselhamento.
          </p>
          <p>
            Se o(a) Sr.(a) aceitar participar da pesquisa, os procedimentos envolvidos em sua
            participação são os seguintes: Coleta de dados anônimos sobre o perfil do estudante e
            sua avaliação do material didático utilizado nos componentes curriculares de introdução
            à programação Campus Igarassu.
          </p>
          <p>
            Toda pesquisa com seres humanos envolve algum tipo de risco. No nosso estudo, os
            possíveis riscos ou desconfortos decorrentes da participação na pesquisa são quebra do
            sigilo e confidencialidade dos dados.
          </p>
          <p>
            Contudo, esta pesquisa também pode trazer benefícios. Os possíveis benefícios
            resultantes da participação na pesquisa são melhoria do material didático nos
            componentes curriculares de introdução à programação Campus Igarassu e democratização do
            conhecimento sobre o que se estuda nos cursos de tecnologia da informação do Campus
            Igarassu.
          </p>
          <p>
            Sua participação na pesquisa é totalmente voluntária, ou seja, não é obrigatória. Caso
            o(a) Sr.(a) decida não participar, ou ainda, desistir de participar e retirar seu
            consentimento durante a pesquisa, não haverá nenhum prejuízo à avaliação curricular que
            você recebe ou possa vir a receber na instituição.
          </p>
          <p>
            Não está previsto nenhum tipo de pagamento pela sua participação na pesquisa e o(a)
            Sr.(a) não terá nenhum custo com respeito aos procedimentos envolvidos.
          </p>
          <p>
            Caso ocorra algum problema ou dano com o(a) Sr.(a), resultante de sua participação na
            pesquisa, o(a) Sr.(a) receberá todo o atendimento necessário, sem nenhum custo pessoal e
            garantimos indenização diante de eventuais fatos comprovados, com nexo causal com a
            pesquisa.
          </p>
          <p>
            Solicitamos também sua autorização para apresentar os resultados deste estudo em eventos
            das áreas dos cursos do Campus Igarassu, em revista científica nacional e/ou
            internacional. Por ocasião da publicação dos resultados, seus dados pessoais serão
            mantidos em sigilo absoluto, bem como em todas fases da pesquisa.
          </p>
          <p>
            É assegurada a assistência durante toda pesquisa, bem como é garantido ao Sr.(a), o
            livre acesso a todas as informações e esclarecimentos adicionais sobre o estudo e suas
            consequências, enfim, tudo o que o(a) Sr.(a) queira saber antes, durante e depois da sua
            participação.
          </p>
          <p>
            Caso o(a) Sr.(a) tenha dúvidas, poderá entrar em contato com o pesquisador responsável
            Allan Diego Silva Lima, pelo telefone 81 998581583, endereço BR-101, KM 29, Igarassu -
            PE, 53659-899 e/ou pelo e-mail allan.lima@igarassu.ifpe.edu.br.
          </p>
        </div>
      </div>
      <p class="consent-confirm">
        Ao pressionar o botão abaixo, você concorda em participar do estudo e autoriza o uso dos
        dados inseridos neste aplicativo para fins de pesquisa.
      </p>
      <button class="consent-button" @click="handleConsent">Eu concordo</button>
    </div>
  </div>

  <!-- Main App Screen -->
  <div class="app-container" v-else>
    <div class="background-pattern"></div>

    <div class="container">
      <header class="header">
        <div class="header-icon">🧠</div>
        <h1>Comparador de Respostas Terapêuticas</h1>
        <p class="subtitle">
          Compare diferentes abordagens terapêuticas e escolha a mais adequada para você
        </p>
      </header>

      <div class="input-section">
        <div class="input-wrapper">
          <label for="prompt-input" class="input-label">
            <span class="label-text">Descreva sua situação ou emoção</span>
            <span class="label-hint"
              >Seja o mais específico possível para obter melhores respostas</span
            >
          </label>
          <textarea
            id="prompt-input"
            v-model="prompt"
            placeholder="Ex: Estou me sentindo ansioso sobre uma apresentação importante no trabalho..."
            class="prompt-textarea"
          ></textarea>
        </div>

        <button @click="getResponses" :disabled="loading || !prompt.trim()" class="generate-btn">
          <span v-if="loading" class="btn-loading">
            <div class="btn-spinner"></div>
            Analisando...
          </span>
          <span v-else class="btn-content">
            <span class="btn-icon">✨</span>
            Obter Respostas
          </span>
        </button>
      </div>

      <div v-if="loading" class="loading-section" ref="loadingSection" tabindex="-1">
        <div class="loading-animation">
          <div class="loading-dots">
            <div></div>
            <div></div>
            <div></div>
          </div>
        </div>
        <div class="loading-text">
          <h3>Analisando seu prompt...</h3>
          <p>Gerando respostas personalizadas com diferentes abordagens terapêuticas</p>
        </div>
      </div>

      <div
        v-else-if="responses.length"
        class="responses-section"
        ref="responsesSection"
        tabindex="-1"
      >
        <div class="instructions-card">
          <div class="instructions-icon">🔍</div>
          <div class="instructions-content">
            <h3>Como escolher a melhor resposta</h3>
            <ul>
              <li>Leia ambas as respostas cuidadosamente</li>
              <li>Considere qual abordagem ressoa mais com você</li>
              <li>Clique na resposta que considera mais útil</li>
              <li>Confirme sua escolha clicando no botão "Confirmar Resposta"</li>
            </ul>
          </div>
        </div>

        <div class="responses-grid">
          <div
            v-for="(response, index) in responses"
            :key="index"
            class="response-card"
            @click="selectResponse(response.type)"
            :class="{
              selected: selectedResponse === response.type,
              'fade-out': selectedResponse && selectedResponse !== response.type,
              disabled: hasConfirmed,
            }"
          >
            <div class="response-header">
              <div class="response-badge">
                <span class="badge-number">{{ index + 1 }}</span>
                <span class="badge-text">Abordagem {{ index + 1 }}</span>
              </div>
              <div v-if="selectedResponse === response.type" class="selected-icon">
                <div class="checkmark-circle">✓</div>
              </div>
            </div>

            <div class="response-content">
              <div class="content-text" v-html="formatText(response.content)"></div>
            </div>

            <div class="response-footer">
              <div class="response-actions">
                <span v-if="!hasConfirmed" class="action-hint">
                  {{
                    selectedResponse === response.type
                      ? 'Resposta selecionada'
                      : 'Clique para selecionar esta resposta'
                  }}
                </span>
                <span v-else class="action-hint">
                  {{
                    selectedResponse === response.type ? 'Resposta confirmada' : 'Não selecionada'
                  }}
                </span>
              </div>
            </div>
          </div>
        </div>

        <div v-if="selectedResponse && !hasConfirmed" class="confirmation-section">
          <div class="confirmation-card">
            <div class="confirmation-icon">⚠️</div>
            <div class="confirmation-content">
              <h3>Confirmar sua escolha</h3>
              <p>
                Você selecionou uma resposta. Deseja confirmar esta escolha? Esta ação não poderá
                ser desfeita.
              </p>
            </div>
            <div class="confirmation-actions">
              <button @click="confirmResponse" :disabled="confirmingVote" class="confirm-btn">
                <span v-if="confirmingVote" class="btn-loading">
                  <div class="btn-spinner"></div>
                  Confirmando...
                </span>
                <span v-else class="btn-content">
                  <span class="btn-icon">✅</span>
                  Confirmar Resposta
                </span>
              </button>
              <button @click="cancelSelection" class="cancel-btn">
                <span class="btn-icon">❌</span>
                Cancelar
              </button>
            </div>
          </div>
        </div>
      </div>

      <div v-if="voted" class="success-section" ref="successSection" tabindex="-1">
        <div class="success-animation">
          <div class="success-circle">
            <div class="checkmark">✓</div>
          </div>
        </div>
        <div class="success-content">
          <h2>Feedback Registrado!</h2>
          <p>
            Obrigado por sua avaliação. Seu feedback é valioso para melhorar nossos serviços de
            assistência terapêutica.
          </p>
          <div class="success-actions">
            <button @click="reset" class="reset-btn">
              <span class="btn-icon">🔄</span>
              Nova Comparação
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import MarkdownIt from 'markdown-it'

export default {
  data() {
    return {
      consentGiven: false,
      showConsentText: false,
      prompt: '',
      responses: [],
      loading: false,
      voted: false,
      selectedResponse: null,
      hasConfirmed: false,
      confirmingVote: false,
    }
  },
  methods: {
    toggleConsentText() {
      this.showConsentText = !this.showConsentText
    },
    handleConsent() {
      this.consentGiven = true
      localStorage.setItem('consentGiven', 'true')
    },
    shuffleArray(array) {
      const shuffled = [...array]
      for (let i = shuffled.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1))
        ;[shuffled[i], shuffled[j]] = [shuffled[j], shuffled[i]]
      }
      return shuffled
    },
    async getResponses() {
      if (!this.prompt.trim()) return

      this.loading = true
      this.responses = []
      this.selectedResponse = null
      this.hasConfirmed = false
      this.voted = false

      // Foca na seção de loading após iniciar o carregamento
      await this.$nextTick()
      if (this.$refs.loadingSection) {
        this.$refs.loadingSection.focus()
        this.$refs.loadingSection.scrollIntoView({
          behavior: 'smooth',
          block: 'center',
        })
      }

      try {
        const response = await axios.post(`${import.meta.env.VITE_API_URL}/api/responses`, {
          prompt: this.prompt,
        })
        // Embaralha as respostas para garantir anonimato
        this.responses = this.shuffleArray(response.data.responses)

        // Foca na seção de respostas após carregar
        await this.$nextTick()
        if (this.$refs.responsesSection) {
          this.$refs.responsesSection.focus()
          this.$refs.responsesSection.scrollIntoView({
            behavior: 'smooth',
            block: 'center',
          })
        }
      } catch (error) {
        console.error(error)
        alert('Erro ao gerar respostas. Por favor, tente novamente.')
      } finally {
        this.loading = false
      }
    },
    detectChatGptMarkdown(text) {
      const patterns = [
        /^```/, // code blocks
        /^#{1,6}\s/, // cabeçalhos
        /^[-*+]\s/, // listas
        /\*\*(.*?)\*\*/, // negrito
        /\*(.*?)\*/, // itálico
        /`([^`]+)`/, // código inline
        /\[.*?\]\(.*?\)/, // links
        /^\d+\.\s/, // listas numeradas
        /^>\s/, // blockquotes
      ]

      return patterns.some((pattern) => pattern.test(text))
    },
    formatText(text) {
      if (this.detectChatGptMarkdown(text)) {
        return new MarkdownIt().render(text)
      }
      const paragraphs = text
        .split(/\n{2,}/)
        .map(
          (block) =>
            `<p>${block
              .split('\n')
              .map((line) => line.trim())
              .join('<br/>')}</p>`,
        )
        .join('')
      return paragraphs
    },
    selectResponse(responseType) {
      if (this.hasConfirmed) return
      this.selectedResponse = responseType
    },
    cancelSelection() {
      this.selectedResponse = null
    },
    async confirmResponse() {
      if (!this.selectedResponse || this.hasConfirmed) return

      this.confirmingVote = true

      try {
        await axios.post(`${import.meta.env.VITE_API_URL}/api/votes`, {
          prompt: this.prompt,
          chosenResponseType: this.selectedResponse,
          responses: this.responses,
        })

        this.hasConfirmed = true
        this.voted = true

        await this.$nextTick()

        if (this.$refs.successSection) {
          this.$refs.successSection.focus()
          this.$refs.successSection.scrollIntoView({
            behavior: 'smooth',
            block: 'center',
          })
        }
      } catch (error) {
        console.error(error)
        alert('Erro ao registrar sua escolha. Por favor, tente novamente.')
      } finally {
        this.confirmingVote = false
      }
    },
    reset() {
      this.prompt = ''
      this.responses = []
      this.voted = false
      this.selectedResponse = null
      this.hasConfirmed = false
      this.confirmingVote = false
    },
  },
  // mounted() {
  //   // Verificar localStorage para manter consentimento em futuras visitas
  //   if (localStorage.getItem('consentGiven') === 'true') {
  //     this.consentGiven = true
  //   }
  // },
}
</script>

<style>
:root {
  --primary: #667eea;
  --primary-light: #764ba2;
  --primary-dark: #5a67d8;
  --secondary: #48bb78;
  --secondary-light: #68d391;
  --accent: #ed8936;
  --warning: #f6ad55;
  --danger: #f56565;
  --text-primary: #2d3748;
  --text-secondary: #4a5568;
  --text-muted: #718096;
  --background: #f7fafc;
  --surface: #ffffff;
  --surface-elevated: #ffffff;
  --border: #e2e8f0;
  --border-light: #f1f5f9;
  --shadow-sm: 0 1px 3px rgba(0, 0, 0, 0.1);
  --shadow-md: 0 4px 6px rgba(0, 0, 0, 0.1);
  --shadow-lg: 0 10px 15px rgba(0, 0, 0, 0.1);
  --shadow-xl: 0 20px 25px rgba(0, 0, 0, 0.1);
  --radius-sm: 8px;
  --radius-md: 12px;
  --radius-lg: 16px;
  --radius-xl: 24px;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family:
    -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  line-height: 1.6;
  color: var(--text-primary);
  background: var(--background);
}

/* Consent Screen */
.consent-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background: var(--background);
  padding: 1rem;
}
.consent-content {
  background: var(--surface);
  padding: 2rem;
  border-radius: 1rem;
  max-width: 600px;
  text-align: center;
  overflow-y: auto;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}
.consent-content h2 {
  font-size: 1.5rem;
  margin-bottom: 1rem;
}
.consent-content .toggle-consent {
  font-size: 1.1rem;
  font-weight: bold;
  margin: 2rem auto;
  width: 60%;
  border: 2px solid var(--primary);
  padding: 0.5rem;
  border-radius: 16px;
  cursor: pointer;
}
.consent-content p {
  font-size: 1rem;
  margin: 1rem auto;
}
.consent-text {
  text-align: left;
}
.consent-content .consent-confirm {
  font-size: 1rem;
  margin: 1rem auto;
  font-weight: bold;
}
.consent-dropdown {
  margin-top: 1rem;
  transition: max-height 0.3s ease;
}
.consent-button {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: #fff;
  width: 70%;
  margin-top: 1.5rem;
  padding: 0.75rem 1.5rem;
  font-size: 1.25rem;
  font-weight: bold;
  border: none;
  border-radius: 1rem;
  cursor: pointer;
}

/* Container and Layout */
.app-container {
  min-height: 100vh;
  position: relative;
  overflow-x: hidden;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  padding: 2rem 0;
}

.background-pattern {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  opacity: 0.05;
  z-index: -1;
}

.container {
  max-width: 1200px;
  width: 100%;
  margin: 0 auto;
  padding: 0 2rem;
  position: relative;
  z-index: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
}

/* Header Styles */
.header {
  text-align: center;
  margin-bottom: 3rem;
  padding: 2rem 0;
}

.header-icon {
  font-size: 3rem;
  margin-bottom: 1rem;
  display: inline-block;
  animation: pulse 2s infinite;
}

@keyframes pulse {
  0%,
  100% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.05);
  }
}

.header h1 {
  font-size: clamp(2rem, 4vw, 3rem);
  font-weight: 700;
  background: linear-gradient(135deg, var(--primary), var(--primary-light));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  margin-bottom: 0.5rem;
}

.subtitle {
  font-size: 1.1rem;
  color: var(--text-muted);
  max-width: 600px;
  margin: 0 auto;
}

/* Input Section */
.input-section {
  background: var(--surface);
  border-radius: var(--radius-xl);
  padding: 2.5rem;
  box-shadow: var(--shadow-lg);
  margin-bottom: 3rem;
  border: 1px solid var(--border-light);
  width: 100%;
  max-width: 800px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.input-wrapper {
  margin-bottom: 2rem;
  width: 100%;
}

.input-label {
  display: block;
  margin-bottom: 1rem;
}

.label-text {
  display: block;
  font-weight: 600;
  font-size: 1.1rem;
  color: var(--text-primary);
  margin-bottom: 0.25rem;
}

.label-hint {
  display: block;
  font-size: 0.9rem;
  color: var(--text-muted);
}

.prompt-textarea {
  width: 100%;
  min-height: 150px;
  padding: 1.5rem;
  border: 2px solid var(--border);
  border-radius: var(--radius-md);
  font-size: 1rem;
  font-family: inherit;
  color: var(--text-primary);
  resize: vertical;
  transition: all 0.3s ease;
  background: var(--surface);
}

.prompt-textarea:focus {
  outline: none;
  border-color: var(--primary);
  box-shadow: 0 0 0 4px rgba(102, 126, 234, 0.1);
}

.generate-btn {
  width: 100%;
  max-width: 300px;
  padding: 1.2rem 2rem;
  background: linear-gradient(135deg, var(--primary), var(--primary-light));
  color: white;
  border: none;
  border-radius: var(--radius-md);
  font-size: 1.1rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
  box-shadow: var(--shadow-md);
}

.generate-btn:hover:not(:disabled) {
  transform: translateY(-2px);
  box-shadow: var(--shadow-lg);
}

.generate-btn:disabled {
  opacity: 0.7;
  cursor: not-allowed;
  transform: none;
}

.btn-loading,
.btn-content {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.btn-spinner {
  width: 20px;
  height: 20px;
  border: 2px solid rgba(255, 255, 255, 0.3);
  border-top: 2px solid white;
  border-radius: 50%;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

/* Loading Section */
.loading-section {
  text-align: center;
  padding: 4rem 2rem;
  background: var(--surface);
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-lg);
  margin-bottom: 3rem;
  width: 100%;
  max-width: 600px;
  display: flex;
  flex-direction: column;
  align-items: center;
  outline: none;
  scroll-margin-top: 2rem;
}

.loading-section:focus {
  outline: 2px solid var(--primary);
  outline-offset: 4px;
}

.loading-animation {
  margin-bottom: 2rem;
}

.loading-dots {
  display: flex;
  justify-content: center;
  gap: 0.5rem;
}

.loading-dots div {
  width: 12px;
  height: 12px;
  background: var(--primary);
  border-radius: 50%;
  animation: bounce 1.4s infinite ease-in-out both;
}

.loading-dots div:nth-child(1) {
  animation-delay: -0.32s;
}
.loading-dots div:nth-child(2) {
  animation-delay: -0.16s;
}

@keyframes bounce {
  0%,
  80%,
  100% {
    transform: scale(0);
  }
  40% {
    transform: scale(1);
  }
}

.loading-text h3 {
  font-size: 1.5rem;
  color: var(--text-primary);
  margin-bottom: 0.5rem;
}

.loading-text p {
  color: var(--text-muted);
}

/* Responses Section */
.responses-section {
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  outline: none;
  scroll-margin-top: 2rem;
}

.responses-section:focus {
  outline: 2px solid var(--primary);
  outline-offset: 4px;
  border-radius: var(--radius-lg);
}

/* Instructions Card */
.instructions-card {
  background: linear-gradient(135deg, var(--primary), var(--primary-light));
  color: white;
  padding: 2rem;
  border-radius: var(--radius-xl);
  margin-bottom: 2rem;
  display: flex;
  align-items: flex-start;
  gap: 1.5rem;
  box-shadow: var(--shadow-lg);
  width: 100%;
  max-width: 800px;
}

.instructions-icon {
  font-size: 2rem;
  flex-shrink: 0;
}

.instructions-content h3 {
  font-size: 1.3rem;
  margin-bottom: 1rem;
}

.instructions-content ul {
  list-style: none;
  padding: 0;
}

.instructions-content li {
  padding: 0.3rem 0;
  position: relative;
  padding-left: 1.2rem;
}

.instructions-content li:before {
  content: '•';
  position: absolute;
  left: 0;
  color: rgba(255, 255, 255, 0.8);
}

/* Responses Grid */
.responses-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(500px, 1fr));
  gap: 2rem;
  margin-bottom: 2rem;
  width: 100%;
  max-width: 1200px;
  justify-items: center;
}

.response-card {
  background: var(--surface);
  border: 2px solid var(--border);
  border-radius: var(--radius-xl);
  padding: 2rem;
  cursor: pointer;
  transition: all 0.4s ease;
  position: relative;
  overflow: hidden;
  box-shadow: var(--shadow-md);
}

.response-card:hover:not(.disabled) {
  transform: translateY(-5px);
  box-shadow: var(--shadow-xl);
  border-color: var(--primary);
}

.response-card.selected {
  border-color: var(--secondary);
  background: linear-gradient(135deg, rgba(72, 187, 120, 0.05), rgba(104, 211, 145, 0.05));
  transform: translateY(-3px);
}

.response-card.fade-out {
  opacity: 0.6;
  transform: scale(0.98);
}

.response-card.disabled {
  cursor: not-allowed;
  opacity: 0.8;
}

.response-card.disabled:hover {
  transform: none;
  box-shadow: var(--shadow-md);
  border-color: var(--border);
}

.response-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1.5rem;
  padding-bottom: 1rem;
  border-bottom: 1px solid var(--border-light);
}

.response-badge {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.badge-number {
  background: var(--primary);
  color: white;
  width: 30px;
  height: 30px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 700;
  font-size: 0.9rem;
}

.badge-text {
  font-weight: 600;
  color: var(--text-primary);
}

.selected-icon {
  animation: slideInRight 0.5s ease;
}

.checkmark-circle {
  background: var(--secondary);
  color: white;
  width: 32px;
  height: 32px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 700;
  animation: scaleIn 0.3s ease;
}

@keyframes slideInRight {
  from {
    transform: translateX(20px);
    opacity: 0;
  }
  to {
    transform: translateX(0);
    opacity: 1;
  }
}

@keyframes scaleIn {
  from {
    transform: scale(0.5);
  }
  to {
    transform: scale(1);
  }
}

.response-content {
  margin-bottom: 1.5rem;
}

.content-text {
  /* tipografia agradável */
  font-family: 'Open Sans', sans-serif;
  font-size: 1rem;
  line-height: 1.6;
}

/* parágrafos já com espaçamento */
.content-text p {
  margin-bottom: 1.2em;
}

/* títulos */
.content-text h1,
.content-text h2,
.content-text h3,
.content-text h4 {
  font-family: 'Montserrat', sans-serif;
  font-weight: 600;
  margin: 1.6em 0 0.6em;
  line-height: 1.25;
}
.content-text h1 {
  font-size: 2em;
}
.content-text h2 {
  font-size: 1.75em;
}
.content-text h3 {
  font-size: 1.5em;
}
.content-text h4 {
  font-size: 1.25em;
}

/* listas */
.content-text ul,
.content-text ol {
  margin: 1em 0 1em 1.5em;
}
.content-text li + li {
  margin-top: 0.5em;
}

/* links */
.content-text a {
  color: #0366d6;
  text-decoration: none;
  border-bottom: 1px dashed transparent;
  transition: border-color 0.2s;
}
.content-text a:hover {
  border-color: currentColor;
}

/* blockquotes */
.content-text blockquote {
  border-left: 4px solid #ddd;
  border-radius: 3px;
  padding: 4px;
  margin: 1.2em 0;
  background: #fafafa;
  color: #555;
  font-style: italic;
  text-align: center;
}

/* código inline e blocos */
.content-text code {
  background: #555;
  padding: 0.2em 0.4em;
  border-radius: 3px;
  font-family: 'Source Code Pro', monospace;
  font-size: 0.95em;
}
.content-text pre {
  background: #2d2d2d;
  color: #f8f8f2;
  padding: 1em;
  border-radius: 4px;
  overflow-x: auto;
  margin: 1.5em 0;
}
.content-text pre code {
  background: transparent;
  padding: 0;
  color: inherit;
  font-size: 0.9em;
}

/* imagens */
.content-text img {
  max-width: 100%;
  display: block;
  margin: 1.5em auto;
  border-radius: 4px;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
}

.response-footer {
  padding-top: 1rem;
  border-top: 1px solid var(--border-light);
}

.response-actions {
  display: flex;
  justify-content: center;
  align-items: center;
}

.action-hint {
  color: var(--text-muted);
  font-size: 0.9rem;
  font-style: italic;
}

/* Confirmation Section */
.confirmation-section {
  width: 100%;
  max-width: 800px;
  margin-bottom: 3rem;
}

.confirmation-card {
  background: var(--surface);
  border: 2px solid var(--warning);
  border-radius: var(--radius-xl);
  padding: 2rem;
  box-shadow: var(--shadow-lg);
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  animation: slideInUp 0.5s ease;
}

@keyframes slideInUp {
  from {
    transform: translateY(20px);
    opacity: 0;
  }
  to {
    transform: translateY(0);
    opacity: 1;
  }
}

.confirmation-icon {
  font-size: 3rem;
  margin-bottom: 1rem;
  animation: pulse 2s infinite;
}

.confirmation-content {
  margin-bottom: 2rem;
}

.confirmation-content h3 {
  font-size: 1.5rem;
  color: var(--text-primary);
  margin-bottom: 1rem;
}

.confirmation-content p {
  color: var(--text-muted);
  line-height: 1.6;
  max-width: 500px;
}

.confirmation-actions {
  display: flex;
  gap: 1rem;
  flex-wrap: wrap;
  justify-content: center;
}

.confirm-btn {
  background: var(--secondary);
  color: white;
  border: none;
  padding: 1rem 2rem;
  border-radius: var(--radius-md);
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  gap: 0.5rem;
  box-shadow: var(--shadow-md);
}

.confirm-btn:hover:not(:disabled) {
  background: var(--secondary-light);
  transform: translateY(-2px);
  box-shadow: var(--shadow-lg);
}

.confirm-btn:disabled {
  opacity: 0.7;
  cursor: not-allowed;
  transform: none;
}

.cancel-btn {
  background: var(--danger);
  color: white;
  border: none;
  padding: 1rem 2rem;
  border-radius: var(--radius-md);
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  gap: 0.5rem;
  box-shadow: var(--shadow-md);
}

.cancel-btn:hover {
  background: #e53e3e;
  transform: translateY(-2px);
  box-shadow: var(--shadow-lg);
}

/* Success Section */
.success-section {
  text-align: center;
  padding: 4rem 2rem;
  background: var(--surface);
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-lg);
  animation: fadeInUp 0.8s ease;
  width: 100%;
  max-width: 600px;
  display: flex;
  flex-direction: column;
  align-items: center;
  outline: none;
  scroll-margin-top: 2rem;
}

.success-section:focus {
  outline: 2px solid var(--primary);
  outline-offset: 4px;
}

@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.success-animation {
  margin-bottom: 2rem;
}

.success-circle {
  width: 80px;
  height: 80px;
  background: var(--secondary);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 0 auto;
  animation: bounceIn 0.8s ease;
}

.success-circle .checkmark {
  color: white;
  font-size: 2rem;
  font-weight: 700;
}

@keyframes bounceIn {
  0% {
    transform: scale(0.3);
    opacity: 0;
  }
  50% {
    transform: scale(1.05);
  }
  70% {
    transform: scale(0.9);
  }
  100% {
    transform: scale(1);
    opacity: 1;
  }
}

.success-content h2 {
  font-size: 2rem;
  color: var(--text-primary);
  margin-bottom: 1rem;
}

.success-content p {
  color: var(--text-muted);
  max-width: 500px;
  margin: 0 auto 2rem;
  line-height: 1.7;
}

.reset-btn {
  background: var(--accent);
  color: white;
  border: none;
  padding: 1rem 2rem;
  border-radius: var(--radius-md);
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  box-shadow: var(--shadow-md);
}

.reset-btn:hover {
  background: #e07628;
  transform: translateY(-2px);
  box-shadow: var(--shadow-lg);
}

/* Responsive Design */
@media (max-width: 1024px) {
  .responses-grid {
    grid-template-columns: 1fr;
    max-width: 600px;
  }

  .instructions-card {
    flex-direction: column;
    text-align: center;
    max-width: 600px;
  }

  .confirmation-actions {
    flex-direction: column;
    align-items: center;
  }

  .confirm-btn,
  .cancel-btn {
    width: 100%;
    max-width: 250px;
  }
}

@media (max-width: 768px) {
  .app-container {
    padding: 1rem 0;
  }

  .container {
    padding: 0 1rem;
  }

  .input-section {
    padding: 1.5rem;
  }

  .header {
    margin-bottom: 2rem;
    padding: 1rem 0;
  }

  .header h1 {
    font-size: 2rem;
  }

  .prompt-textarea {
    min-height: 120px;
    padding: 1rem;
  }

  .response-card {
    padding: 1.5rem;
  }

  .instructions-card {
    padding: 1.5rem;
  }

  .success-section {
    padding: 3rem 1.5rem;
  }

  .responses-grid {
    max-width: 100%;
  }

  .confirmation-card {
    padding: 1.5rem;
  }
}

@media (max-width: 480px) {
  .responses-grid {
    grid-template-columns: 1fr;
    gap: 1rem;
  }

  .response-card {
    padding: 1rem;
  }

  .header h1 {
    font-size: 1.8rem;
  }

  .generate-btn {
    padding: 1rem;
  }

  .input-section {
    padding: 1rem;
  }

  .confirmation-actions {
    gap: 0.5rem;
  }

  .confirm-btn,
  .cancel-btn {
    padding: 0.8rem 1.5rem;
    font-size: 0.9rem;
  }
}

/* Dark mode support */
@media (prefers-color-scheme: dark) {
  :root {
    --text-primary: #f7fafc;
    --text-secondary: #e2e8f0;
    --text-muted: #a0aec0;
    --background: #1a202c;
    --surface: #2d3748;
    --surface-elevated: #4a5568;
    --border: #4a5568;
    --border-light: #2d3748;
  }
}
</style>

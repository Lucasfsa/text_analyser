<script setup>
import { ref } from 'vue'
import axios from 'axios'
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';

const typeForm = ref('simple')
const simplePhraseResponse = ref(null)
const multiplePhraseResponse = ref(null)
const simplePhraseForm = ref(null)
const multiplePhraseForm = ref(null)
const phrases = ref(null)
const loading = ref(false)
const initialize = ref(true)

const axiosInstance = axios.create({
  baseURL: 'https://textanalyserbackend.lucasazevedo2.repl.co/',
  headers: {
    'Content-Type': 'application/json',
    'Accept': '*/*'
  }
});


function alternate(type) {
  initialize.value = true
  phrases.value = null
  simplePhraseResponse.value = null
  multiplePhraseResponse.value = null
  typeForm.value = type
}

async function sendSimplePhrase() {

  if (phrases.value == null) {
    alert("Campo obrigatório")
  } else {
    loading.value = true
    initialize.value = false

    await axiosInstance.post("/sentiment", {
      "pre_processed_text": phrases.value
    })
      .then(function (response) {
        simplePhraseResponse.value = response.data.prediction
      })
      .catch(function (error) {
        console.log(error)
      })

    simplePhraseForm.value = phrases.value
    phrases.value = null
    loading.value = false
  }
}

async function sendMultiplePhrases() {
  if (phrases.value == null) {
    alert("Campo obrigatório")
  } else {
    loading.value = true
    initialize.value = false

    await axiosInstance.post("/process-sentiments", {
      "pre_processed_text": phrases.value
    })
      .then(function (response) {
        multiplePhraseResponse.value = response.data.prediction
      })
      .catch(function (error) {
        console.log(error)
      })

    multiplePhraseForm.value = phrases.value
    phrases.value = null
    loading.value = false
  }
}

</script>

<template>
  <div class="container d-flex justify-content-center">
    <div class="row">
      <div class="col-sm-6 div-intro">
        <div>
          <div class="d-flex justify-content-center">
            <h1 class="green">Text Analyser</h1>
          </div>
          <div class="intro-body">
            <h3>
              Esta é uma ferramenta que tem a capacidade de analisar sentimentos textuais. O seu objetivo é analisar um
              texto, e através das palavras chaves identificadas, conseguir classificar a emoção presente no texto.
            </h3>
            <h3>
              O texto pode ser classificado como muito positivo, positivo, neutro, negativo ou muito negativo.
            </h3>
            <h3>
              São duas opções disponíveis, a análise de texto simples e análise de multíplos textos.
            </h3>
          </div>

        </div>

        <div class="mt-3">
          <h3>Alterne entre as opções:</h3>

          <div class="btn-group btn-group-toggle mt-3 d-flex justify-content-center" data-toggle="buttons">

            <label class="btn btn-green btn-lg active" @click="alternate('simple')">
              <input type="radio" name="options" id="option2" autocomplete="off"> Texto Simples
            </label>
            <label class="btn btn-green btn-lg" @click="alternate('multiple')">
              <input type="radio" name="options" id="option3" autocomplete="off"> Multiplos Textos
            </label>
          </div>
        </div>
      </div>

      <div class="col-sm-6">
        <div v-if="typeForm === 'simple'">
          <div class="simple-form">
            <div class="d-flex justify-content-center mb-2">
              <h3>Envio de texto simples</h3>
            </div>
            <h3>O envio de texto simples classifica o tipo de emoção presente no texto.</h3>
            <div class="input-group  size-input mt-3">
              <div class="input-group-prepend">
              </div>
              <input v-model="phrases" type="text" class="form-control" placeholder="Digite o texto..."
                aria-label="Usuário">
            </div>
            <div class="d-flex flex-row-reverse mt-4">
              <button class="btn btn-green btn-lg" @click="sendSimplePhrase">Enviar</button>
            </div>
          </div>


          <div class="simple-response mt-2">
            <div class="d-flex justify-content-center default-response" v-if="initialize == true">
              <svg xmlns="http://www.w3.org/2000/svg" height="2em"
                viewBox="0 0 448 512"><!--! Font Awesome Free 6.4.2 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2023 Fonticons, Inc. -->
                <path
                  d="M8 256a56 56 0 1 1 112 0A56 56 0 1 1 8 256zm160 0a56 56 0 1 1 112 0 56 56 0 1 1 -112 0zm216-56a56 56 0 1 1 0 112 56 56 0 1 1 0-112z" />
              </svg>
            </div>
            <div v-else class="table-wrapper-scroll-y my-custom-scrollbar">
              <div class="d-flex justify-content-center default-response" v-if="loading == true">
                <font-awesome-icon :icon="['fas', 'spinner']" spin-pulse size="2x" />
              </div>
              <div v-else>
                <div class="d-flex justify-content-center mb-2">
                  <h3>Classificação</h3>
                </div>
                <table class="table table-bordered mb-0">

                  <tbody>
                    <tr>
                      <th scope="row">Texto</th>
                      <td>{{ simplePhraseForm }}</td>

                    </tr>
                    <tr>
                      <th scope="row">Sentimento</th>
                      <td>{{ simplePhraseResponse }}</td>

                    </tr>
                    <tr>
                      <th scope="row">Avaliação</th>
                      <td>
                        <div v-if="simplePhraseResponse == 'muito negativo'">
                          <svg xmlns="http://www.w3.org/2000/svg" height="2em" viewBox="0 0 576 512">
                            <path
                              d="M287.9 0c9.2 0 17.6 5.2 21.6 13.5l68.6 141.3 153.2 22.6c9 1.3 16.5 7.6 19.3 16.3s.5 18.1-5.9 24.5L433.6 328.4l26.2 155.6c1.5 9-2.2 18.1-9.6 23.5s-17.3 6-25.3 1.7l-137-73.2L151 509.1c-8.1 4.3-17.9 3.7-25.3-1.7s-11.2-14.5-9.7-23.5l26.2-155.6L31.1 218.2c-6.5-6.4-8.7-15.9-5.9-24.5s10.3-14.9 19.3-16.3l153.2-22.6L266.3 13.5C270.4 5.2 278.7 0 287.9 0zm0 79L235.4 187.2c-3.5 7.1-10.2 12.1-18.1 13.3L99 217.9 184.9 303c5.5 5.5 8.1 13.3 6.8 21L171.4 443.7l105.2-56.2c7.1-3.8 15.6-3.8 22.6 0l105.2 56.2L384.2 324.1c-1.3-7.7 1.2-15.5 6.8-21l85.9-85.1L358.6 200.5c-7.8-1.2-14.6-6.1-18.1-13.3L287.9 79z" />
                          </svg>
                          <svg xmlns="http://www.w3.org/2000/svg" height="2em" viewBox="0 0 576 512">
                            <path
                              d="M287.9 0c9.2 0 17.6 5.2 21.6 13.5l68.6 141.3 153.2 22.6c9 1.3 16.5 7.6 19.3 16.3s.5 18.1-5.9 24.5L433.6 328.4l26.2 155.6c1.5 9-2.2 18.1-9.6 23.5s-17.3 6-25.3 1.7l-137-73.2L151 509.1c-8.1 4.3-17.9 3.7-25.3-1.7s-11.2-14.5-9.7-23.5l26.2-155.6L31.1 218.2c-6.5-6.4-8.7-15.9-5.9-24.5s10.3-14.9 19.3-16.3l153.2-22.6L266.3 13.5C270.4 5.2 278.7 0 287.9 0zm0 79L235.4 187.2c-3.5 7.1-10.2 12.1-18.1 13.3L99 217.9 184.9 303c5.5 5.5 8.1 13.3 6.8 21L171.4 443.7l105.2-56.2c7.1-3.8 15.6-3.8 22.6 0l105.2 56.2L384.2 324.1c-1.3-7.7 1.2-15.5 6.8-21l85.9-85.1L358.6 200.5c-7.8-1.2-14.6-6.1-18.1-13.3L287.9 79z" />
                          </svg>
                          <svg xmlns="http://www.w3.org/2000/svg" height="2em" viewBox="0 0 576 512">
                            <path
                              d="M287.9 0c9.2 0 17.6 5.2 21.6 13.5l68.6 141.3 153.2 22.6c9 1.3 16.5 7.6 19.3 16.3s.5 18.1-5.9 24.5L433.6 328.4l26.2 155.6c1.5 9-2.2 18.1-9.6 23.5s-17.3 6-25.3 1.7l-137-73.2L151 509.1c-8.1 4.3-17.9 3.7-25.3-1.7s-11.2-14.5-9.7-23.5l26.2-155.6L31.1 218.2c-6.5-6.4-8.7-15.9-5.9-24.5s10.3-14.9 19.3-16.3l153.2-22.6L266.3 13.5C270.4 5.2 278.7 0 287.9 0zm0 79L235.4 187.2c-3.5 7.1-10.2 12.1-18.1 13.3L99 217.9 184.9 303c5.5 5.5 8.1 13.3 6.8 21L171.4 443.7l105.2-56.2c7.1-3.8 15.6-3.8 22.6 0l105.2 56.2L384.2 324.1c-1.3-7.7 1.2-15.5 6.8-21l85.9-85.1L358.6 200.5c-7.8-1.2-14.6-6.1-18.1-13.3L287.9 79z" />
                          </svg>
                          <svg xmlns="http://www.w3.org/2000/svg" height="2em" viewBox="0 0 576 512">
                            <path
                              d="M287.9 0c9.2 0 17.6 5.2 21.6 13.5l68.6 141.3 153.2 22.6c9 1.3 16.5 7.6 19.3 16.3s.5 18.1-5.9 24.5L433.6 328.4l26.2 155.6c1.5 9-2.2 18.1-9.6 23.5s-17.3 6-25.3 1.7l-137-73.2L151 509.1c-8.1 4.3-17.9 3.7-25.3-1.7s-11.2-14.5-9.7-23.5l26.2-155.6L31.1 218.2c-6.5-6.4-8.7-15.9-5.9-24.5s10.3-14.9 19.3-16.3l153.2-22.6L266.3 13.5C270.4 5.2 278.7 0 287.9 0zm0 79L235.4 187.2c-3.5 7.1-10.2 12.1-18.1 13.3L99 217.9 184.9 303c5.5 5.5 8.1 13.3 6.8 21L171.4 443.7l105.2-56.2c7.1-3.8 15.6-3.8 22.6 0l105.2 56.2L384.2 324.1c-1.3-7.7 1.2-15.5 6.8-21l85.9-85.1L358.6 200.5c-7.8-1.2-14.6-6.1-18.1-13.3L287.9 79z" />
                          </svg>
                        </div>
                        <div v-if="simplePhraseResponse == 'negativo'">
                          <svg xmlns="http://www.w3.org/2000/svg" height="2em" viewBox="0 0 576 512">
                            <path
                              d="M316.9 18C311.6 7 300.4 0 288.1 0s-23.4 7-28.8 18L195 150.3 51.4 171.5c-12 1.8-22 10.2-25.7 21.7s-.7 24.2 7.9 32.7L137.8 329 113.2 474.7c-2 12 3 24.2 12.9 31.3s23 8 33.8 2.3l128.3-68.5 128.3 68.5c10.8 5.7 23.9 4.9 33.8-2.3s14.9-19.3 12.9-31.3L438.5 329 542.7 225.9c8.6-8.5 11.7-21.2 7.9-32.7s-13.7-19.9-25.7-21.7L381.2 150.3 316.9 18z" />
                          </svg>
                          <svg xmlns="http://www.w3.org/2000/svg" height="2em" viewBox="0 0 576 512">
                            <path
                              d="M287.9 0c9.2 0 17.6 5.2 21.6 13.5l68.6 141.3 153.2 22.6c9 1.3 16.5 7.6 19.3 16.3s.5 18.1-5.9 24.5L433.6 328.4l26.2 155.6c1.5 9-2.2 18.1-9.6 23.5s-17.3 6-25.3 1.7l-137-73.2L151 509.1c-8.1 4.3-17.9 3.7-25.3-1.7s-11.2-14.5-9.7-23.5l26.2-155.6L31.1 218.2c-6.5-6.4-8.7-15.9-5.9-24.5s10.3-14.9 19.3-16.3l153.2-22.6L266.3 13.5C270.4 5.2 278.7 0 287.9 0zm0 79L235.4 187.2c-3.5 7.1-10.2 12.1-18.1 13.3L99 217.9 184.9 303c5.5 5.5 8.1 13.3 6.8 21L171.4 443.7l105.2-56.2c7.1-3.8 15.6-3.8 22.6 0l105.2 56.2L384.2 324.1c-1.3-7.7 1.2-15.5 6.8-21l85.9-85.1L358.6 200.5c-7.8-1.2-14.6-6.1-18.1-13.3L287.9 79z" />
                          </svg>
                          <svg xmlns="http://www.w3.org/2000/svg" height="2em" viewBox="0 0 576 512">
                            <path
                              d="M287.9 0c9.2 0 17.6 5.2 21.6 13.5l68.6 141.3 153.2 22.6c9 1.3 16.5 7.6 19.3 16.3s.5 18.1-5.9 24.5L433.6 328.4l26.2 155.6c1.5 9-2.2 18.1-9.6 23.5s-17.3 6-25.3 1.7l-137-73.2L151 509.1c-8.1 4.3-17.9 3.7-25.3-1.7s-11.2-14.5-9.7-23.5l26.2-155.6L31.1 218.2c-6.5-6.4-8.7-15.9-5.9-24.5s10.3-14.9 19.3-16.3l153.2-22.6L266.3 13.5C270.4 5.2 278.7 0 287.9 0zm0 79L235.4 187.2c-3.5 7.1-10.2 12.1-18.1 13.3L99 217.9 184.9 303c5.5 5.5 8.1 13.3 6.8 21L171.4 443.7l105.2-56.2c7.1-3.8 15.6-3.8 22.6 0l105.2 56.2L384.2 324.1c-1.3-7.7 1.2-15.5 6.8-21l85.9-85.1L358.6 200.5c-7.8-1.2-14.6-6.1-18.1-13.3L287.9 79z" />
                          </svg>
                          <svg xmlns="http://www.w3.org/2000/svg" height="2em" viewBox="0 0 576 512">
                            <path
                              d="M287.9 0c9.2 0 17.6 5.2 21.6 13.5l68.6 141.3 153.2 22.6c9 1.3 16.5 7.6 19.3 16.3s.5 18.1-5.9 24.5L433.6 328.4l26.2 155.6c1.5 9-2.2 18.1-9.6 23.5s-17.3 6-25.3 1.7l-137-73.2L151 509.1c-8.1 4.3-17.9 3.7-25.3-1.7s-11.2-14.5-9.7-23.5l26.2-155.6L31.1 218.2c-6.5-6.4-8.7-15.9-5.9-24.5s10.3-14.9 19.3-16.3l153.2-22.6L266.3 13.5C270.4 5.2 278.7 0 287.9 0zm0 79L235.4 187.2c-3.5 7.1-10.2 12.1-18.1 13.3L99 217.9 184.9 303c5.5 5.5 8.1 13.3 6.8 21L171.4 443.7l105.2-56.2c7.1-3.8 15.6-3.8 22.6 0l105.2 56.2L384.2 324.1c-1.3-7.7 1.2-15.5 6.8-21l85.9-85.1L358.6 200.5c-7.8-1.2-14.6-6.1-18.1-13.3L287.9 79z" />
                          </svg>
                        </div>
                        <div v-if="simplePhraseResponse == 'neutro'">
                          <svg xmlns="http://www.w3.org/2000/svg" height="2em" viewBox="0 0 576 512">
                            <path
                              d="M316.9 18C311.6 7 300.4 0 288.1 0s-23.4 7-28.8 18L195 150.3 51.4 171.5c-12 1.8-22 10.2-25.7 21.7s-.7 24.2 7.9 32.7L137.8 329 113.2 474.7c-2 12 3 24.2 12.9 31.3s23 8 33.8 2.3l128.3-68.5 128.3 68.5c10.8 5.7 23.9 4.9 33.8-2.3s14.9-19.3 12.9-31.3L438.5 329 542.7 225.9c8.6-8.5 11.7-21.2 7.9-32.7s-13.7-19.9-25.7-21.7L381.2 150.3 316.9 18z" />
                          </svg>
                          <svg xmlns="http://www.w3.org/2000/svg" height="2em" viewBox="0 0 576 512">
                            <path
                              d="M316.9 18C311.6 7 300.4 0 288.1 0s-23.4 7-28.8 18L195 150.3 51.4 171.5c-12 1.8-22 10.2-25.7 21.7s-.7 24.2 7.9 32.7L137.8 329 113.2 474.7c-2 12 3 24.2 12.9 31.3s23 8 33.8 2.3l128.3-68.5 128.3 68.5c10.8 5.7 23.9 4.9 33.8-2.3s14.9-19.3 12.9-31.3L438.5 329 542.7 225.9c8.6-8.5 11.7-21.2 7.9-32.7s-13.7-19.9-25.7-21.7L381.2 150.3 316.9 18z" />
                          </svg>
                          <svg xmlns="http://www.w3.org/2000/svg" height="2em" viewBox="0 0 576 512">
                            <path
                              d="M287.9 0c9.2 0 17.6 5.2 21.6 13.5l68.6 141.3 153.2 22.6c9 1.3 16.5 7.6 19.3 16.3s.5 18.1-5.9 24.5L433.6 328.4l26.2 155.6c1.5 9-2.2 18.1-9.6 23.5s-17.3 6-25.3 1.7l-137-73.2L151 509.1c-8.1 4.3-17.9 3.7-25.3-1.7s-11.2-14.5-9.7-23.5l26.2-155.6L31.1 218.2c-6.5-6.4-8.7-15.9-5.9-24.5s10.3-14.9 19.3-16.3l153.2-22.6L266.3 13.5C270.4 5.2 278.7 0 287.9 0zm0 79L235.4 187.2c-3.5 7.1-10.2 12.1-18.1 13.3L99 217.9 184.9 303c5.5 5.5 8.1 13.3 6.8 21L171.4 443.7l105.2-56.2c7.1-3.8 15.6-3.8 22.6 0l105.2 56.2L384.2 324.1c-1.3-7.7 1.2-15.5 6.8-21l85.9-85.1L358.6 200.5c-7.8-1.2-14.6-6.1-18.1-13.3L287.9 79z" />
                          </svg>
                          <svg xmlns="http://www.w3.org/2000/svg" height="2em" viewBox="0 0 576 512">
                            <path
                              d="M287.9 0c9.2 0 17.6 5.2 21.6 13.5l68.6 141.3 153.2 22.6c9 1.3 16.5 7.6 19.3 16.3s.5 18.1-5.9 24.5L433.6 328.4l26.2 155.6c1.5 9-2.2 18.1-9.6 23.5s-17.3 6-25.3 1.7l-137-73.2L151 509.1c-8.1 4.3-17.9 3.7-25.3-1.7s-11.2-14.5-9.7-23.5l26.2-155.6L31.1 218.2c-6.5-6.4-8.7-15.9-5.9-24.5s10.3-14.9 19.3-16.3l153.2-22.6L266.3 13.5C270.4 5.2 278.7 0 287.9 0zm0 79L235.4 187.2c-3.5 7.1-10.2 12.1-18.1 13.3L99 217.9 184.9 303c5.5 5.5 8.1 13.3 6.8 21L171.4 443.7l105.2-56.2c7.1-3.8 15.6-3.8 22.6 0l105.2 56.2L384.2 324.1c-1.3-7.7 1.2-15.5 6.8-21l85.9-85.1L358.6 200.5c-7.8-1.2-14.6-6.1-18.1-13.3L287.9 79z" />
                          </svg>
                        </div>
                        <div v-if="simplePhraseResponse == 'positivo'">
                          <svg xmlns="http://www.w3.org/2000/svg" height="2em" viewBox="0 0 576 512">
                            <path
                              d="M316.9 18C311.6 7 300.4 0 288.1 0s-23.4 7-28.8 18L195 150.3 51.4 171.5c-12 1.8-22 10.2-25.7 21.7s-.7 24.2 7.9 32.7L137.8 329 113.2 474.7c-2 12 3 24.2 12.9 31.3s23 8 33.8 2.3l128.3-68.5 128.3 68.5c10.8 5.7 23.9 4.9 33.8-2.3s14.9-19.3 12.9-31.3L438.5 329 542.7 225.9c8.6-8.5 11.7-21.2 7.9-32.7s-13.7-19.9-25.7-21.7L381.2 150.3 316.9 18z" />
                          </svg>
                          <svg xmlns="http://www.w3.org/2000/svg" height="2em" viewBox="0 0 576 512">
                            <path
                              d="M316.9 18C311.6 7 300.4 0 288.1 0s-23.4 7-28.8 18L195 150.3 51.4 171.5c-12 1.8-22 10.2-25.7 21.7s-.7 24.2 7.9 32.7L137.8 329 113.2 474.7c-2 12 3 24.2 12.9 31.3s23 8 33.8 2.3l128.3-68.5 128.3 68.5c10.8 5.7 23.9 4.9 33.8-2.3s14.9-19.3 12.9-31.3L438.5 329 542.7 225.9c8.6-8.5 11.7-21.2 7.9-32.7s-13.7-19.9-25.7-21.7L381.2 150.3 316.9 18z" />
                          </svg>
                          <svg xmlns="http://www.w3.org/2000/svg" height="2em" viewBox="0 0 576 512">
                            <path
                              d="M316.9 18C311.6 7 300.4 0 288.1 0s-23.4 7-28.8 18L195 150.3 51.4 171.5c-12 1.8-22 10.2-25.7 21.7s-.7 24.2 7.9 32.7L137.8 329 113.2 474.7c-2 12 3 24.2 12.9 31.3s23 8 33.8 2.3l128.3-68.5 128.3 68.5c10.8 5.7 23.9 4.9 33.8-2.3s14.9-19.3 12.9-31.3L438.5 329 542.7 225.9c8.6-8.5 11.7-21.2 7.9-32.7s-13.7-19.9-25.7-21.7L381.2 150.3 316.9 18z" />
                          </svg>
                          <svg xmlns="http://www.w3.org/2000/svg" height="2em" viewBox="0 0 576 512">
                            <path
                              d="M287.9 0c9.2 0 17.6 5.2 21.6 13.5l68.6 141.3 153.2 22.6c9 1.3 16.5 7.6 19.3 16.3s.5 18.1-5.9 24.5L433.6 328.4l26.2 155.6c1.5 9-2.2 18.1-9.6 23.5s-17.3 6-25.3 1.7l-137-73.2L151 509.1c-8.1 4.3-17.9 3.7-25.3-1.7s-11.2-14.5-9.7-23.5l26.2-155.6L31.1 218.2c-6.5-6.4-8.7-15.9-5.9-24.5s10.3-14.9 19.3-16.3l153.2-22.6L266.3 13.5C270.4 5.2 278.7 0 287.9 0zm0 79L235.4 187.2c-3.5 7.1-10.2 12.1-18.1 13.3L99 217.9 184.9 303c5.5 5.5 8.1 13.3 6.8 21L171.4 443.7l105.2-56.2c7.1-3.8 15.6-3.8 22.6 0l105.2 56.2L384.2 324.1c-1.3-7.7 1.2-15.5 6.8-21l85.9-85.1L358.6 200.5c-7.8-1.2-14.6-6.1-18.1-13.3L287.9 79z" />
                          </svg>
                        </div>
                        <div v-if="simplePhraseResponse == 'muito positivo'">
                          <svg xmlns="http://www.w3.org/2000/svg" height="2em" viewBox="0 0 576 512">
                            <path
                              d="M316.9 18C311.6 7 300.4 0 288.1 0s-23.4 7-28.8 18L195 150.3 51.4 171.5c-12 1.8-22 10.2-25.7 21.7s-.7 24.2 7.9 32.7L137.8 329 113.2 474.7c-2 12 3 24.2 12.9 31.3s23 8 33.8 2.3l128.3-68.5 128.3 68.5c10.8 5.7 23.9 4.9 33.8-2.3s14.9-19.3 12.9-31.3L438.5 329 542.7 225.9c8.6-8.5 11.7-21.2 7.9-32.7s-13.7-19.9-25.7-21.7L381.2 150.3 316.9 18z" />
                          </svg>
                          <svg xmlns="http://www.w3.org/2000/svg" height="2em" viewBox="0 0 576 512">
                            <path
                              d="M316.9 18C311.6 7 300.4 0 288.1 0s-23.4 7-28.8 18L195 150.3 51.4 171.5c-12 1.8-22 10.2-25.7 21.7s-.7 24.2 7.9 32.7L137.8 329 113.2 474.7c-2 12 3 24.2 12.9 31.3s23 8 33.8 2.3l128.3-68.5 128.3 68.5c10.8 5.7 23.9 4.9 33.8-2.3s14.9-19.3 12.9-31.3L438.5 329 542.7 225.9c8.6-8.5 11.7-21.2 7.9-32.7s-13.7-19.9-25.7-21.7L381.2 150.3 316.9 18z" />
                          </svg>
                          <svg xmlns="http://www.w3.org/2000/svg" height="2em" viewBox="0 0 576 512">
                            <path
                              d="M316.9 18C311.6 7 300.4 0 288.1 0s-23.4 7-28.8 18L195 150.3 51.4 171.5c-12 1.8-22 10.2-25.7 21.7s-.7 24.2 7.9 32.7L137.8 329 113.2 474.7c-2 12 3 24.2 12.9 31.3s23 8 33.8 2.3l128.3-68.5 128.3 68.5c10.8 5.7 23.9 4.9 33.8-2.3s14.9-19.3 12.9-31.3L438.5 329 542.7 225.9c8.6-8.5 11.7-21.2 7.9-32.7s-13.7-19.9-25.7-21.7L381.2 150.3 316.9 18z" />
                          </svg>
                          <svg xmlns="http://www.w3.org/2000/svg" height="2em" viewBox="0 0 576 512">
                            <path
                              d="M316.9 18C311.6 7 300.4 0 288.1 0s-23.4 7-28.8 18L195 150.3 51.4 171.5c-12 1.8-22 10.2-25.7 21.7s-.7 24.2 7.9 32.7L137.8 329 113.2 474.7c-2 12 3 24.2 12.9 31.3s23 8 33.8 2.3l128.3-68.5 128.3 68.5c10.8 5.7 23.9 4.9 33.8-2.3s14.9-19.3 12.9-31.3L438.5 329 542.7 225.9c8.6-8.5 11.7-21.2 7.9-32.7s-13.7-19.9-25.7-21.7L381.2 150.3 316.9 18z" />
                          </svg>
                        </div>
                      </td>
                    </tr>
                  </tbody>
                </table>
              </div>

            </div>
          </div>

        </div>

        <div v-else-if="typeForm === 'multiple'">
          <div class="simple-form">
            <div class="d-flex justify-content-center mb-2">
              <h3>Envio de multiplos textos</h3>
            </div>
            <h3>É obrigatório enviar os textos sepradados por / (barra a direita)</h3>

            <div class="input-group mt-2">
              <div class="input-group-prepend">
              </div>
              <textarea v-model="phrases" type="text" class="form-control" placeholder="Digite os textos..."
                aria-label="Usuário"></textarea>
            </div>
            <div class="d-flex flex-row-reverse mt-3">
              <button class="btn btn-green btn-lg" @click="sendMultiplePhrases">Enviar</button>
            </div>
          </div>

          <div class="simple-response mt-2">
            <div class="d-flex justify-content-center default-response" v-if="initialize == true">
              <svg xmlns="http://www.w3.org/2000/svg" height="2em"
                viewBox="0 0 448 512"><!--! Font Awesome Free 6.4.2 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2023 Fonticons, Inc. -->
                <path
                  d="M8 256a56 56 0 1 1 112 0A56 56 0 1 1 8 256zm160 0a56 56 0 1 1 112 0 56 56 0 1 1 -112 0zm216-56a56 56 0 1 1 0 112 56 56 0 1 1 0-112z" />
              </svg>
            </div>
            <div v-else class="table-wrapper-scroll-y my-custom-scrollbar">
              <div class="d-flex justify-content-center default-response" v-if="loading == true">
                <font-awesome-icon :icon="['fas', 'spinner']" spin-pulse size="2x" />
              </div>
              <div v-else>
                <div class="d-flex justify-content-center mb-2">
                  <h3>Estastísticas</h3>
                </div>

                <table class="table table-bordered mb-0">
                  <thead>
                    <tr>
                      <th scope="col">Muito positivas</th>
                      <th scope="col">Positivas</th>
                      <th scope="col">Neutras</th>
                      <th scope="col">Negativas</th>
                      <th scope="col">Muito negativas</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr>
                      <td>{{ multiplePhraseResponse.very_positive_predictions }}</td>
                      <td>{{ multiplePhraseResponse.positive_predictions }}</td>
                      <td>{{ multiplePhraseResponse.neutro_predictions }}</td>
                      <td>{{ multiplePhraseResponse.negative_predictions }}</td>
                      <td>{{ multiplePhraseResponse.very_negative_predictions }}</td>
                    </tr>
                  </tbody>
                </table>

                <div class="d-flex justify-content-center mb-2 mt-3">
                  <h3>Classificações</h3>
                </div>

                <div>
                  <table class="table table-bordered table-striped mb-0">
                    <thead>
                      <tr>
                        <th scope="col">Texto</th>
                        <th scope="col">Classificação</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr v-for="(predict, value) in multiplePhraseResponse.predicts" :key="value">
                        <th scope="row">{{ value }}</th>
                        <td>{{ predict }}</td>
                      </tr>
                    </tbody>
                  </table>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
@media (min-width: 1024px) {
  .body {
    display: flex;
    align-items: center;
  }
}

h1 {
  font-weight: 500;
  font-size: 2.6rem;
  position: relative;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}


.btn-green {
  background-color: #2EE59D;
  color: #ffffff;
}

.btn-green:hover {
  background-color: #00BD7E;

}

.active {
  background-color: #00BD7E;
  box-shadow: rgba(0, 0, 0, 0.07) 0px 1px 2px, rgba(0, 0, 0, 0.07) 0px 2px 4px, rgba(0, 0, 0, 0.07) 0px 4px 8px, rgba(0, 0, 0, 0.07) 0px 8px 16px, rgba(0, 0, 0, 0.07) 0px 16px 32px, rgba(0, 0, 0, 0.07) 0px 32px 64px;
}

.linha-vertical {
  height: 500px;
  color: #464d4a;
}

.size-input {
  width: 100%;
}

.simple-response {
  height: 255px;
  background-color: rgba(255, 255, 255, 1);
  color: #000;
  border-radius: 5px;
  box-shadow: 0px 12px 28px 0px rgba(140, 149, 159, 0.3);
  width: 100%;
  padding: 15px 15px 15px 15px;

}

.simple-form {
  height: 250px;
  background-color: rgba(255, 255, 255, 1);
  color: #000;
  border-radius: 5px;
  box-shadow: 0px 12px 28px 0px rgba(140, 149, 159, 0.3);
  width: 100%;
  padding: 15px 20px 15px 20px;
}

.div-intro {
  height: 513px;
  background-color: rgba(255, 255, 255, 1);
  color: #000;
  border-radius: 5px;
  box-shadow: 0px 12px 28px 0px rgba(140, 149, 159, 0.3);
  width: 100%;
  padding: 15px 20px 15px 20px;
}

.intro-body {
  background-color: rgb(245, 245, 245);
  border-radius: 5px;
  padding: 5px 5px 5px 5px;
  ;
}

.default-response {
  margin-top: 20%;
}

.multiple-response {
  overflow: auto;
}

.my-custom-scrollbar {
  position: relative;
  height: 95%;
  overflow: auto;
  margin-left: 10px;
}

.table-wrapper-scroll-y {
  display: block;
}

.my-custom-scrollbar::-webkit-scrollbar-track {
  border-radius: 0;
  background-color: #D0D4CE;
}

.my-custom-scrollbar::-webkit-scrollbar {
  width: 6px;
  background-color: #D0D4CE;
}

.my-custom-scrollbar::-webkit-scrollbar-thumb {
  border-radius: 0;
  -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, .3);
  background-color: #2EE59D;
}
</style>
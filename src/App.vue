<script setup>
import { ref, watch } from 'vue';
import axios from 'axios';

const text = ref("Hello. How are you?");
const selectedLanguage = ref("spanish");
const translation = ref("");
const timeout = ref(null);

const languages = [
    { name: "Spanish", value: "spanish" },
    { name: "German", value: "german" },
    { name: "French", value: "french" },
    { name: "Italian", value: "italian" },
    { name: "Danish", value: "danish" }
];

const url = import.meta.env.VITE_API_URL || "http://localhost";

const fetchJeringozaTranslation = async () => {
    try {
        const response = await axios.post(url + "/jeringoza", {
            text: text.value.trim(),
            language: selectedLanguage.value
        });
        translation.value = response.data.translated_text;
    } catch (error) {
        translation.value = "Error: " + error.response.data.detail;
    }
};


const fetchTranslation = async () => {
    try {
        const response = await axios.post(url + "/translate", {
            text: text.value.trim(),
            language: selectedLanguage.value
        });
        translation.value = response.data.translated_text;
    } catch (error) {
        translation.value = "Error: " + error.response.data.detail;
    }
};

watch(text, () => {
    clearTimeout(timeout.value);
    timeout.value = setTimeout(() => {
        fetchTranslation();
    }, 1000);
});

watch(selectedLanguage, () => {
    fetchTranslation();
});
</script>

<template>
  <div class="container">
    <h1>LanguageWire Translator</h1>
    <input v-model="text" placeholder="Enter text..." />
    <select v-model="selectedLanguage">
      <option v-for="lang in languages" :key="lang.value" :value="lang.value">
        {{ lang.name }}
      </option>
    </select>
    <button @click="fetchJeringozaTranslation" class="btn">Translate to Jeringoza</button>
    <p class="result" v-if="!translation.includes('Error')">
      <strong>Translation:</strong><br />{{ translation }}
    </p>
    <p class="result error" v-else>
      <strong>Attention:</strong><br />{{ translation }}
    </p>
  </div>
  <div class="container-logo">
    <img src="/src/assets/logo-big.png" alt="LanguageWire" class="logo" />
  </div>
</template>

<style>
.container {
  max-width: 100%;
  margin: auto;
  padding: 40px;
  text-align: center;
  border-radius: 10px;
}
.container-logo {
  max-width: 50%;
  margin: auto;
  padding: 40px;
  text-align: center;
  border-radius: 10px;
}
input, select {
  margin: 10px;
  padding: 8px;
  width: 100%;
}
.result {
  margin-top: 20px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 8px;
  font-size: 19px;
  color: #FFFFFF;
}
.result strong {
  font-size: 20px;
  color: #CCC;
}
.error {
  background-color: #dc3545;
}
.btn {
  padding: 10px 20px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}
.logo {
  top: 0;
  right: 0;
  padding: 10px;
  width: 400px;
}
/*create nice layout for mobile*/

@media (max-width: 600px) {
  .container {
    padding: 20px;
  }
  .container-logo {
    padding: 20px;
    max-width: 100%;
  }
  .logo {
    width: 200px;
  }
}

</style>
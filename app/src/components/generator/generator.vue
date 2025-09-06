<template>
  <div class="container">
    <div class="answer">
      {{  answer }}
    </div>
    <div class="input-container">
      <input v-model="prompt" class="input" type="text" name="" id="" placeholder="enter prompt">
      <button @click="handlePrompt" class="button" v-bind:disabled="isLoading" >Send</button>
    </div>
  </div>
</template>

<script>
import { GoogleGenerativeAI } from '@google/generative-ai';

export default {
  data() {
    return {
      isLoading: false,
      answer: "", 
      prompt: "",
    };
  },
  name: "GeneratorForm",
  methods: {
    async handlePrompt() {
      const aiPrompt = `answer the following prompt while not exceeding 100 words and separate each option you provide with new line: prompt:${this.prompt}`
      const genAI = new GoogleGenerativeAI(process.env.VUE_APP_GEMINI_API_KEY)
      this.isLoading = true
      try {
        const textResult = await genAI
          .getGenerativeModel({ model: 'gemini-2.0-flash'})
          .generateContent([aiPrompt])
        
          this.answer = textResult.response.text();
      } catch(e) {
        this.answer = "Something went wrong try again"
        console.error('Prompt error', e)
      }
      finally {
        this.isLoading = false
      }
  }
}
}
</script>

<style>
  .container { 
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    position: absolute;
    width: 440px !important;
    height: 60%;
    bottom: 10px;
    right: 5px;
    border: 1px solid #000;
    padding: 1.1rem 0.5rem;
    border-radius: 12px;
  }
  .answer {
    overflow-y: auto;
  }
  .input-container {
    display: flex;
    flex-direction: column;
    align-items: start;
    gap: 20px;
    padding: 1.2rem 0.5rem;
    
  }
  .input {
    width: 100%;
    padding: 1.1rem 0.5rem ;
    border: 1px solid #000;
    height: fit-content;
    overflow-y: auto;
  }
  .button {
    border-radius: 10px;
    padding: 1rem 0.5rem;
    width: 120px;
    background-color: #000 !important;
    color: #fff;
  }
  button:disabled {
    background-color: grey !important;
  }
  button:disabled:hover {
    background-color: grey !important;
  }
  .button:hover {
    background-color: #fff !important;
    color: #000;
    border: 1px solid #000;
  }
</style>
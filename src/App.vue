<template>
  <div id="app">
    <div class="container">
      <h1>Fancy Quote {</h1>
      <div class="content">
        <div class="form">
          <div v-if="currentStep === step[0]">
            <div class="form__item">
              <label for="quote">Quote:</label>
              <textarea 
                v-model="quote"
                name="quote" 
                @change="generate">
              </textarea>
            </div>
            <div class="form__item">
              <label for="author">Author:</label>
              <input 
                type="text" 
                value="" 
                placeholder="Author" 
                v-model="author"
                name="author"
                @change="generate"/>
            </div>
            <div class="form__item">
              <label for="quotation">Quotation:</label>
              <input 
                type="checkbox" 
                v-model="quotation" 
                name="quotation"
                @change="generate"/>
            </div>
          </div>
          
          <div v-if="currentStep === step[1]">
            <div class="form__item">
              <label for="fadeness">Fadeness:</label>
              <div>
                <input 
                  type="range" 
                  value="" 
                  min="1" 
                  max="5" 
                  step="1" 
                  v-model="fadeness" 
                  name="fadeness"
                  @change="generate"/>
                <span>{{this.fadeness}}</span>
              </div>
            </div>
            <div class="form__item">
              <label for="speed">Speed:</label>
              <div>
                <input 
                  type="range" 
                  value="" 
                  min="100" 
                  max="5000" 
                  step="100" 
                  v-model="speed" 
                  name="speed"
                  @change="generate"/>
                <span>{{this.speed}}</span>
              </div>
            </div>
            <div class="form__item">
              <label for="split">Split:</label>
              <div>
                <input 
                  type="range" 
                  value="" 
                  min="0" 
                  max="5" 
                  step="1" 
                  v-model="split" 
                  name="split"
                  @change="generate"/>
                <span>{{this.split}}</span>
              </div>
            </div>
          </div>
          <div class="form__item" v-if="currentStep === step[2]">
            <label for="generated">Generated code:</label>
            <textarea 
              v-model="code"
              name="generated">
            </textarea>
          </div>
          <button @click.prevent="nextStep">{{ currentStepLabel }}</button>
        </div>

        <div>
          <h3>Output</h3>
          <div v-html="this.code"></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data(){
    return{
      quote: "I see now that the circumstances of one's birth are irrelevant; it is what you do with the gift of life that determines who you are.",
      code: '',
      author: 'Mewtwo',
      speed: 1000,
      split: 3,
      fadeness: 1,
      quotation: true,
      step: [0, 1, 2],
      currentStep: 0,
      currentStepLabel: 'Next Step',
      stepLabels: ['Next Step', 'Generate', 'Start Again'],
      generateCode: false
    }
  },
  mounted(){
    this.generate()
  },
  methods: {
    nextStep(){
      this.currentStep = this.currentStep === 2 ? 0 : this.currentStep + 1
      this.currentStepLabel = this.stepLabels[this.currentStep]
    },
    generate(){
      const sentences = this.breakIntoSentences(this.quote)
      const output = this.splitSentences(sentences, Number(this.split))
      let spans = ''
      let css = ''
      let animation = ''
      output.forEach((item, i) => {
        spans += `<span>${i === 0 && this.quotation ? '"' : ''}${item}${i === (output.length-1) && this.quotation ? '"' : ''} </span>`
        css += `#fancy-quote span:nth-child(${i+1}){animation: fade-in ${this.speed}ms ${this.speed * i}ms forwards;}`
      })
      css += `#fancy-quote span:nth-child(${output.length+1}){animation: fade-in ${this.speed}ms ${this.speed * (output.length)}ms forwards;}`
      spans += `<span>${this.author}</span>`

      for (let i = 0; i <= this.fadeness; i++) {
        animation += `${(i / this.fadeness) * 100}% {opacity: ${i / this.fadeness};}`
      }

      let styles = `<style>@keyframes fade-in{${animation}}#fancy-quote{--font-quote: Serif; font-family: var(--font-quote); font-size: 28px; max-width: 400px;}#fancy-quote span{opacity: 0;}${css}#fancy-quote span:last-child{display: block; font-family: var(--font-quote); font-weight: 100; font-size: 0.75em; letter-spacing: -1px;}#fancy-quote span:last-child:before{content: "-"; padding-right: 10px;}</style>`

      let html = `<div id="fancy-quote">${spans}</div>`
      this.code = `${styles}${html}`
    },
    breakIntoSentences(s){
      return s.split(/(?<=[.?!;])\s*/g)
    },
    splitSentences(arr, number){
      console.log(arr, number)
      if(number === 0) {
        return arr
      }
      let newArr = []
      arr.map(sentence => {
        if(sentence.length >= number) {
        const arrSentence = this.splitMyString(sentence, number)
        newArr = newArr.concat(arrSentence)
        }
        return sentence
      })
      return newArr
    },
    splitMyString(str, splitLength){
      const a = str.split(' '), b = [];
      while(a.length) b.push(a.splice(0,splitLength).join(' '));
      return b;
    }
  }
}
</script>

<style>
:root{
  --border-color: rgba(0, 0, 0, 0.1);
  --bg-color: rgba(0, 0, 0, 0.01);
  --font-color: #333;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: var(--font-color);
  margin-top: 60px;
}

.container {
  padding-right: 15px;
  padding-left: 15px;
  margin-right: auto;
  margin-left: auto;
}

@media (min-width: 768px) {
  .container {
    width: 750px;
    padding-right: 0;
    padding-left: 0;
  }
}

@media (min-width: 992px) {
  .container {
    width: 970px;
  }
}

@keyframes floating1 {
    from { transform: translate(0,  0px); }
    65%  { transform: translate(-1px, 15px); }
    to   { transform: translate(0, -0px); }    
}
@keyframes floating2 {
    from { transform: translate(0,  0px); }
    33%  { transform: translate(-2px, 10px); }
    66%  { transform: translate(-5px, 15px); }
    to   { transform: translate(0, -0px); }    
}
@keyframes floating3 {
    from { transform: translate(0,  0px); }
    33%  { transform: translate(-1px, 10px); }
    66%  { transform: translate(-3px, 15px); }
    to   { transform: translate(0, -0px); }   
}

.floating-one{
  animation: floating1 10s infinite ease-in-out;
}

.floating-two{
  animation: floating2 10s infinite ease-in-out;
}

.floating-three{
  animation: floating3 10s infinite ease-in-out;
}

.content{
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-column-gap: 50px;
}

.form__item{
  text-align: left;
  display: grid;
  grid-template-columns: 80px 1fr;
  grid-column-gap: 10px;
  padding: 10px 0;
}

.form__item label{
  text-align: right;
}

body textarea,
body input{
  border: 1px solid var(--border-color);
  background-color: var(--bg-color);
  font-size: 12px;
  padding: 10px;
  line-height: 18px;
  border-radius: 3px;
  color: var(--font-color);
}

body textarea{
  resize: inherit;
  min-height: 100px;
}
</style>

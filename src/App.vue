<template>
  <div id="app">
    <div class="container">
      <h1>Fancy Quote <span class="highlight">}</span></h1>
      <div id="fancy-quote-1"><span>To be amused at </span><span>what you read - </span><span>that is the great </span><span>spring of quotation. </span><span>Charles Edward Montague</span></div>
      <div class="content">
        <div class="form">
          <div v-if="currentStep === step[0]" class="step">
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
          
          <div v-if="currentStep === step[1]" class="step">
            <div class="form__item">
              <label for="fadeness">Fadeness:</label>
              <div class="form__range">
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
              <div class="form__range">
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
              <div class="form__range">
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
          <div class="form__item step" v-if="currentStep === step[2]">
            <label for="generated">Generated code:</label>
            <textarea 
              v-model="code"
              name="generated"
              ref="generated"
              @click="copyContent">
            </textarea>
            <div :class="['copy', {'copy--show' : showCopiedTip}]">
              <span>Copied!</span>
            </div>
          </div>
          <div class="control">
            <button v-show="currentStep !== 0" @click.prevent="previousStep" class="previous">
              <span>Previous</span>
            </button>
            <button v-show="currentStep < 2" @click.prevent="nextStep" class="next">
              <span>Next</span>
            </button>
          </div>
        </div>

        <div class="output">
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
      generateCode: false,
      showCopiedTip: false
    }
  },
  mounted(){
    this.generate(false)
  },
  methods: {
    nextStep(){
      this.currentStep += 1
    },
    previousStep(){
      this.currentStep -= 1
    },
    generate(noAnimation){
      const sentences = this.breakIntoSentences(this.quote)
      const output = this.splitSentences(sentences, Number(this.split))
      const speed = Number(this.speed)
      let spans = ''
      let css = ''
      let animation = ''
      output.forEach((item, i) => {
        spans += `<span>${i === 0 && this.quotation ? '"' : ''}${item}${i === (output.length-1) && this.quotation ? '"' : ''} </span>`
        css += `#fancy-quote span:nth-child(${i+1}){animation: fade-in ${speed}ms ${speed * i}ms forwards;}`
      })
      const authorSpeed = speed > 500 ? speed : speed + 1000
      css += `#fancy-quote span:nth-child(${output.length+1}){animation: fade-in ${ authorSpeed }ms ${speed * output.length + (authorSpeed / 2)}ms forwards;}`
      spans += `<span>${this.author}</span>`

      for (let i = 0; i <= this.fadeness; i++) {
        animation += `${(i / this.fadeness) * 100}% {opacity: ${i / this.fadeness};}`
      }
      let opacity = 0
      if (!noAnimation) {
        animation= ''
        opacity = 1
      }
      let styles = `<style>@keyframes fade-in{${animation}}#fancy-quote span{opacity: ${opacity};}${css}#fancy-quote span:last-child{display: block; font-weight: 100; font-size: 0.75em; letter-spacing: -1px;}#fancy-quote span:last-child:before{content: "-"; padding-right: 10px;}</style>`
      let html = `<div id="fancy-quote">${spans}</div>`
      this.code = `${styles}${html}`
    },
    breakIntoSentences(s){
      return s.split(/(?<=[.?!;])\s*/g)
    },
    splitSentences(arr, number){
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
    },
    copyContent() {
      const textarea = this.$refs.generated;
      textarea.select();
      document.execCommand("copy");
      this.showCopiedTip = true;
      setTimeout(() => {
        this.showCopiedTip = false;
      }, 3000);
    },
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css?family=Titillium+Web:200');

:root{
  --highlight-color: #269459;
  --border-color: rgba(0, 0, 0, 0.1);
  --bg-color: rgba(0, 0, 0, 0.01);
  --font-color: #333;
  --font-quote: 'Titillium Web', serif;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: var(--font-color);
}

@media (min-width: 800px) {
  #app {
    margin-top: 60px;
  }
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

.content{
  min-height: 280px;
}

@media (min-width: 800px) {
  .content{
    display: grid;
    grid-template-columns: 1fr 1fr;
    grid-column-gap: 50px;
    align-items: center;
    min-height: 280px;
  }
}

.highlight{
  color: var(--highlight-color);
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

.form__range{
  display: grid;
  grid-template-columns: 1fr 50px;
  grid-column-gap: 10px;
  align-items: center;
}

.form textarea,
.form input{
  border: 1px solid var(--border-color);
  background-color: var(--bg-color);
  font-size: 12px;
  padding: 10px;
  line-height: 18px;
  border-radius: 3px;
  color: var(--font-color);
}

.form textarea{
  resize: inherit;
  min-height: 100px;
}

.form button{
  position: relative;
  background: transparent;
  border: 0;
  cursor: pointer;
  padding: 6px 15px;
}

.form button.next:after{
  content: "";
  position: absolute;
  top: 8px;
  right: -5px;
  width: 0;
  height: 0;
  border-top: 7px solid transparent;
  border-left: 9px solid var(--highlight-color);
  border-bottom: 7px solid transparent;
}

.form button.next:before{
  content: "";
  position: absolute;
  top: 8px;
  right: 1px;
  width: 17px;
  height: 14px;
  background-color: #fff;
  z-index: 1;
  border-radius: 50%;
}

.form button.previous:after{
  content: "";
  position: absolute;
  top: 8px;
  left: -5px;
  width: 0;
  height: 0;
  border-top: 7px solid transparent;
  border-right: 9px solid var(--highlight-color);
  border-bottom: 7px solid transparent;
}

.form button.previous:before{
  content: "";
  position: absolute;
  top: 8px;
  left: 1px;
  width: 17px;
  height: 14px;
  background-color: #fff;
  z-index: 1;
  border-radius: 50%;
}

.form button span{
  position: relative;
  z-index: 2;
}

.control{
  display: grid;
  grid-template-columns: 80px 1fr 80px;
}

.control .previous{
  grid-column: 1;
}

.control .next{
  grid-column: 3;
}

.copy{
  grid-column: 2;
  text-align: right;
  opacity: 0;
  font-size: .75rem;
  padding: 5px 0;
  transition: .5s opacity ease-in-out;
}

.copy--show{
  opacity: 1;
}

.output #fancy-quote{
  font-family: var(--font-quote);
  font-size: 1.5rem;
  line-height: 2.25rem;
}

.output h3{
  margin-top: 0;
}

@keyframes fancy-quote-1{
  0% {opacity: 0;}
  100% {opacity: 1;}
}

#fancy-quote-1 span{opacity: 0;}
#fancy-quote-1 span:nth-child(1){animation: fancy-quote-1 900ms 0ms forwards;}
#fancy-quote-1 span:nth-child(2){animation: fancy-quote-1 900ms 900ms forwards;}
#fancy-quote-1 span:nth-child(3){animation: fancy-quote-1 900ms 1800ms forwards;}
#fancy-quote-1 span:nth-child(4){animation: fancy-quote-1 900ms 2700ms forwards;}
#fancy-quote-1 span:nth-child(5){animation: fancy-quote-1 900ms 4050ms forwards;}
#fancy-quote-1 span:last-child{display: block; font-weight: 100; font-size: 0.75em; letter-spacing: -1px;}
#fancy-quote-1 span:last-child:before{content: "-"; padding-right: 10px;}

body #fancy-quote,
#fancy-quote-1{
  margin: 10px auto;
}
</style>
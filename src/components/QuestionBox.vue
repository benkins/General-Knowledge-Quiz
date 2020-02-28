<template>
    <div>
            <b-jumbotron header="General Knowledge" lead="Bootstrap v4 Components for Vue.js 2">
                  <template v-slot:lead>
                      
                      {{ currentQuestion.question }}

                  </template>
            
                  <hr class="my-4">

                    <b-list-group>
                        <b-list-group-item v-for="(answer, index) in shuffledAnswers" 
                        :key="index"
                        @click.prevent="selectAnswer(index)"
                        :class="answerClass(index)"
                        >
                           {{ answer }}
                        </b-list-group-item>

                    </b-list-group>
            
                  <b-button 
                  variant="primary" 
                  @click= "submitAnswer"
                  :disabled="selectedIndex === null || answered"
                  >
                  Submit
                  </b-button>

                  <b-button 
                  @click= "next" 
                  variant="success"
                  :disabled="!answered"
                  >
                  Next
                  </b-button>
            </b-jumbotron>

    </div>
</template>

<script>
import _ from 'lodash'
export default{

    props: {
        currentQuestion: Object,
        next: Function,
        increment: Function
    },

    data: function(){
        return{
            selectedIndex: null,
            correctIndex: null,
            shuffledAnswers: [],
            answered: false
        }
    },

    computed: {
        answers() {
            let answers = [...this.currentQuestion.incorrect_answers]
            answers.push(this.currentQuestion.correct_answer)
            return answers
        }
    },

    watch: {
        currentQuestion: {
            immediate: true,
            handler() {
                this.selectedIndex = null
                this.answered = false
                this.shuffleAnswers()
            }
        }

    },

    methods: {
        selectAnswer(index) {
            this.selectedIndex = index
            console.log(index)
        },
        submitAnswer() {
            let isCorrect = false
            if(this.selectedIndex === this.correctIndex){
                isCorrect = true
            }
            this.answered = true
            this.increment(isCorrect)
        },  
        shuffleAnswers() {
            let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
            this.shuffledAnswers = _.shuffle(answers)
            this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer) //here is getting the normal index answer not shuffeld index
        },
        answerClass(index) {
            let answerClass = ''

            if(!this.answered && this.selectedIndex === index){
                answerClass = 'selected-answer'
            }else if(this.answered && this.correctIndex === index){
                answerClass = 'correct-answer'
            }else if(this.answered && this.selectedIndex === index && this.correctIndex !== index){
                answerClass = 'incorrect-answer'
            }

            return answerClass        
        }
        

    }

}
</script>

<style scoped>
    .list-group{
        margin-bottom:15px;
    }
    
   .list-group-item:hover{
        background:#EEE;
        cursor: pointer;
    }

    .btn{
        margin:5px;
    }

    .selected-answer{
        background-color: lightblue;
    }

    .correct-answer{
        background-color: lightgreen;
    }

    .incorrect-answer{
        background-color: lightcoral;
    }

</style>
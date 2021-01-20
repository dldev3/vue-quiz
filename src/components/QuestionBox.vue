<template>
    <div class="question-box-container">
    <b-jumbotron>
                <template #lead>
                   {{ currentQuestion.question }}
                </template>
                <hr class="my-4">

                <b-list-group class="mb-3">
                    <b-list-group-item 
                        v-for="(answer,index) in answers" 
                        :key="index"
                        @click="selectAnswer(index)"
                        :class="answerClass(index)"
                        >
                        {{ answer }}
                    </b-list-group-item>
                </b-list-group>
                    
                <b-button 
                    class="mr-1" 
                    variant="primary"
                    @click="submitAnswer()"
                    :disabled="selectedIndex === null || answered"
                >
                    Submit
                </b-button>


                <b-button 
                    @click="next()" 
                    class="ml-1" 
                    variant="success" 
                >
                    Next Question
                </b-button>


    </b-jumbotron>
    </div>
</template>

<script>
import _ from 'lodash';

export default {
    props: {
        currentQuestion: Object,
        next: Function,
        increment: Function
    },
    data: function(){
       return {
           selectedIndex: null,
           correctIndex: null,
           shuffledAnswers: [],
           answered: false
       }
    },
    computed:{
        answers(){
           let answers =  [...this.currentQuestion.incorrect_answers];
           answers.push(this.currentQuestion.correct_answer);
           return answers; 
        }
    },
    watch:{
        currentQuestion: {
            immediate: true,
            handler: function(){
                this.selectedIndex = null;
                this.answered = false;
                this.shuffleAnswers();
            }
        }
        // (){
        //     this.selectedIndex = null;
        //     this.shuffleAnswers();
        // }
    },
    methods: {
        selectAnswer(index){
            this.selectedIndex = index;
        },
        shuffleAnswers(){
        let answers =  [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer];
        this.shuffledAnswers = _.shuffle(answers);

        },
        submitAnswer: function() {
            this.answered = true;
            let isCorrect = false;
            this.correctIndex = this.answers.indexOf(this.currentQuestion.correct_answer);
            if(this.selectedIndex  === this.correctIndex){
                isCorrect = true
            }

            this.increment(isCorrect)
        },
        answerClass: function(index){
            let answerClass = '';
            if(!this.answered && this.selectedIndex == index){
                answerClass = 'selected';
            } else if (this.answered && this.correctIndex === index){
                 answerClass = 'correct';
            } else if (this.answered && this.selectedIndex === index && this.correctIndex !== index){
                 answerClass = 'incorrect';
            } else {
                 answerClass = '';
            }
         return answerClass;
        }
    },
}
</script>

<style lang="css" scoped>
    .list-group-item {
        margin-bottom: 15px;
    }

    .list-group-item:hover{
        cursor: pointer;
        background-color: lightblue;
    }

    .selected {
        background-color: lightblue;
    }

    .correct {
        background-color:lightgreen;
    }

    .incorrect {
        background-color: lightcoral;
    }

    
</style>
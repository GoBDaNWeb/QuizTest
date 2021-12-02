<template>
    <div class="">
        <QuizComplited
            v-if="questionCounter === questions.length"
            :correct="countCorrectAnswer"
            :allQuestion="questions.length"
        />
        <div>
            <div 
                class="text-2xl flex gap-y-6 flex-col justify-center items-center h-screen" 
            >
                <div class="text-2xl">
                    {{currentQuestion.question}}
                </div>
                <div 
                    class="flex flex-col"
                    v-for="choice, index in currentQuestion.choice"
                    :key="index"
                >
                    <div 
                        class="flex items-center border gap-6 w-96 cursor-pointer"
                        @click="selectAnswer(choice, index + 1)"
                        :ref="optionChosen"
                    >
                        <div class="bg-blue-200 px-4 py-2 w-12">
                            {{index + 1}}
                        </div>
                        {{choice}}
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import { ref } from '@vue/reactivity';
import { onMounted } from '@vue/runtime-core';
import QuizComplited from './QuizComplited.vue'
export default {
    components: {
        QuizComplited
    },
    setup() {

        const countCorrectAnswer = ref(0)
        const questionCounter = ref(0)
        let questions = ref([])
        let canClick = true
        let itemsRef = []
        const currentQuestion = ref({
            question: '',
            answer: 1,
            choice: []
        })

        const loadQuestion = () => {
            canClick = true
            if (questions.value.length > questionCounter.value ) {
                questionCounter.value++
                currentQuestion.value = questions.value[questionCounter.value - 1]
                console.log(currentQuestion.value);
                console.log(questions.value[questionCounter.value]);
            } else {
                console.log('Out');
            }
        }

        const clearSelect = (div) => {
            setTimeout(() => {
                div.classList.remove('option-correct')
                div.classList.remove('option-wrong')
                loadQuestion()
            }, 1000)
        }

        const optionChosen = (el) => {
            if (el) {
                itemsRef.push(el)
            }
        }

        const selectAnswer = (answer, index) => {
            if (canClick) {
                let xhr = new XMLHttpRequest();
                let url = "http://localhost:3000/";
                xhr.open("POST", url, true);
                let data = JSON.stringify({ "answer": answer});
                xhr.send(data);
                const div = itemsRef[index - 1]
                if (index === currentQuestion.value.answer) {
                    console.log('YES');
                    countCorrectAnswer.value++
                    div.classList.add('option-correct')
                } else {
                    div.classList.add('option-wrong')
                }
                canClick = false
                clearSelect(div)
            } else {
                console.log('cant select');
            }
        }

        const fetchQuestionsFromServer = async () => {
        fetch('https://opentdb.com/api.php?amount=10&category=9&difficulty=easy&type=multiple')
        .then((res) => {
            return res.json();
        })
        .then((loadedQuestions) => {
            console.log(loadedQuestions)
            questions.value = loadedQuestions.results.map((loadedQuestion) => {
                const formattedQuestion = {
                    question: loadedQuestion.question,
                };
                console.log(formattedQuestion)
                

                const answerChoices = [...loadedQuestion.incorrect_answers];
                formattedQuestion.answer = Math.floor(Math.random() * 4) + 1;
                answerChoices.splice(
                    formattedQuestion.answer - 1,
                    0,
                    loadedQuestion.correct_answer
                );
                console.log(answerChoices)

                formattedQuestion['choice'] = answerChoices;
                return formattedQuestion
            });
            console.log(questions.value)
                        loadQuestion()

            })
            .catch((err) => {
                console.error(err);
            });
        }
        
        onMounted(() => {
            fetchQuestionsFromServer()
        })

        return {countCorrectAnswer, optionChosen, questions, selectAnswer, currentQuestion, questionCounter}

    }
}
</script>
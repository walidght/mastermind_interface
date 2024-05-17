<script setup>
import { ref, watch } from 'vue';
import axios from '../axiosInstance';

const props = defineProps({
    level: String,
    turns: Number,
    showAI: Boolean,
});

console.log('AIBoard ');
console.log(props.level);

const guesses = ref([]);
const feedbacks = ref([]);
const rerender = ref(false);
const printedGuesses = ref([]);

watch(
    () => props.level,
    async (newLevel) => {
        const secretString = localStorage.getItem('secret');
        const secretCode = secretString ? JSON.parse(secretString) : '';
        const currentTurns = props.turns ?? 0;
        const game = await axios
            .post('/ai', {
                code: secretCode,
                level: newLevel,
            })
            .then((response) => response.data)
            .then(({ guesses: newGuesses, feedbacks: newFeedbacks }) => {
                guesses.value = newGuesses;
                feedbacks.value = newFeedbacks;

                printedGuesses.value = [];
                const currentTurns = props.showAI ? 10 : props.turns ?? 0;
                for (let i = 0; i < currentTurns; i++) {
                    if (i >= guesses.value.length) break;
                    if (props.showAI)
                        printedGuesses.value.push(guesses.value[i]);
                    else
                        printedGuesses.value.push([
                            'gray',
                            'gray',
                            'gray',
                            'gray',
                        ]);
                }

                console.log(guesses.value);
            })
            .catch((err) => console.log(err));
    }
);
watch(
    () => props.turns,
    async (newTurns) => {
        printedGuesses.value = [];
        const currentTurns = newTurns ? newTurns : 0;
        for (let i = 0; i < currentTurns; i++) {
            if (i >= guesses.value.length) break;
            if (props.showAI) printedGuesses.value.push(guesses.value[i]);
            else printedGuesses.value.push(['gray', 'gray', 'gray', 'gray']);
        }
        rerender.value = !rerender;
    }
);
watch(
    () => props.showAI,
    (newVal) => {
        printedGuesses.value = [];
        const currentTurns = props.turns ? props.turns : 0;
        for (let i = 0; i < guesses.value.length; i++) {
            printedGuesses.value.push(guesses.value[i]);
        }
    }
);
</script>

<template>
    <div class="container">
        <div class="sub-container" v-if="props.level != 'none'">
            <div v-for="(code, index) in printedGuesses" class="row">
                <div class="code">
                    <div
                        v-for="(color, index) in code"
                        :class="`circle ${color}`"
                        :key="index"
                    ></div>
                </div>
                <div class="feedback">
                    <div
                        v-for="feedbackColor in feedbacks[index]"
                        :class="`circle ${feedbackColor}`"
                    ></div>
                    <div
                        v-for="feedbackColor in 4 - feedbacks[index]?.length"
                        class="circle gray"
                    ></div>
                </div>
            </div>
            <div v-for="el in 10 - printedGuesses?.length" class="row">
                <div class="code">
                    <div class="circle gray"></div>
                    <div class="circle gray"></div>
                    <div class="circle gray"></div>
                    <div class="circle gray"></div>
                </div>
                <div class="feedback">
                    <div class="circle gray"></div>
                    <div class="circle gray"></div>
                    <div class="circle gray"></div>
                    <div class="circle gray"></div>
                </div>
            </div>
            <h1>AI</h1>
        </div>
    </div>
</template>

<style scoped>
.sub-container > h1 {
    color: #02001f;
    margin-left: 3rem;
    margin-bottom: 1rem;
}

.sub-container {
    display: flex;
    flex-direction: column-reverse;
    flex-grow: 1;
    justify-content: space-between;
}
.container {
    height: 100%;
    padding: 3rem 2rem;
    flex: 1;
    display: flex;
    flex-direction: column;
}

.row {
    display: flex;
    justify-content: space-evenly;
    align-items: center;
}

.code {
    display: flex;
    gap: 0.7rem;
}

.feedback {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    grid-auto-rows: 1fr;
    gap: 0.3rem;
}

.code > .circle {
    width: 2.2rem;
    height: 2.2rem;
    border-radius: 50%;
}

.feedback > .circle {
    width: 1rem;
    height: 1rem;
    border-radius: 50%;
    border-width: 1px;
}
</style>

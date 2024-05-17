<script setup>
import DropDown from './DropDown.vue';

import { ref, computed } from 'vue';
import axios from '../axiosInstance.js';

const props = defineProps({
    guesses: Array,
    feedbacks: Array,
    turns: Number,
    updateAILevel: Function,
    level: String,
    updateTurns: Function,
    updateShowAI: Function,
    updateMinGuesses: Function,
});

const selected = ref('green');
const secretCode = ref(null);
const hints = ref([]);
const codeIsShowing = ref(false);
const newGuessCode = ref(['', '', '', '']);
const won = ref(false);

const guessButtonClick = async () => {
    if (won.value) {
        return alert('You already won!');
    }
    if (newGuessCode.value.includes('')) {
        return alert('Please fill all the colors in the guess code!');
    }
    props.guesses.push(newGuessCode.value);
    newGuessCode.value = ['', '', '', ''];
    const feedback = await axios
        .post('/guess', {
            answer: secretCode.value,
            guess: props.guesses[props.guesses.length - 1],
            guesses:props.guesses,
            feedbacks: props.feedbacks,

        })
        .then((response) => response.data)
        .catch((err) => console.log(err));

    props.feedbacks.push(feedback.colors);

    props.updateMinGuesses(feedback.max_remaining);

    hints.value = await axios
        .post('/hint', {
            guesses: props.guesses,
            feedbacks: props.feedbacks,
        })
        .then((response) => response.data)
        .catch((err) => console.log(err));

    props.updateTurns();

    if (feedback.won) {
        codeIsShowing.value = true;
        won.value = true;
        alert('You won!');
    }

    if (feedback.won || props.turns == 9) {
        props.updateShowAI(true);
    }
};

const newGuessCodeClick = (position) => {
    newGuessCode.value[position] = selected.value;
};
const colorButtonClick = (color) => {
    selected.value = color;
};
const showCodeClick = () => {
    console.log('show code');
    codeIsShowing.value = true;
};

const hintClick = () => {
    if (won.value) {
        return alert('You already won!');
    }
    const hint = hints.value.shift();
    hints.value.push(hint);
    if (!hint) return alert('Please make at least one guess first!');
    alert(hint);
};

const getNewCode = async () => {
    return axios
        .get('/code')
        .then((res) => res.data)
        .then((code) => {
            localStorage.setItem('secret', JSON.stringify(code));
            return code;
        })
        .catch((err) => console.log(err));
};

const reloadPage = () => {
    window.location.reload(); // Reload the page
};
const colorButtonClass = (color) =>
    `color-circle ${color} ${selected.value == color ? 'selected' : ''}`;

const secretCodeClass = (position) => {
    return `color-circle ${
        codeIsShowing.value ? secretCode?.value[position] : 'gray'
    }`;
};

const newGuessCodeClass = (position) => {
    return `color-circle ${
        newGuessCode.value[position] != ''
            ? newGuessCode.value[position]
            : 'gray'
    }`;
};

if (secretCode.value == null) {
    secretCode.value = await getNewCode();
}
</script>

<template>
    <div class="container">
        <div class="rest-hint-container">
            <button @click="reloadPage">Reset</button>
            <button @:click="hintClick">Hint</button>
        </div>
        <h1>Code</h1>
        <div class="secret-container">
            <div
                v-for="(color, index) in secretCode"
                :class="secretCodeClass(index)"
            ></div>
            <button @:click="showCodeClick" class="small">Show</button>
        </div>
        <h1>AI Level</h1>
        <DropDown :level="props.level" :updateAILevel="props.updateAILevel" />
        <div class="guess-container">
            <button
                v-for="(color, index) in newGuessCode"
                :class="newGuessCodeClass(index)"
                @:click="newGuessCodeClick(index)"
            ></button>
            <button @:click="guessButtonClick()" class="guess-button">
                Guess
            </button>
        </div>
        <div class="colors-container">
            <button
                @:click="colorButtonClick('green')"
                :class="colorButtonClass('green')"
            ></button>
            <button
                @:click="colorButtonClick('yellow')"
                :class="colorButtonClass('yellow')"
            ></button>
            <button
                @:click="colorButtonClick('blue')"
                :class="colorButtonClass('blue')"
            ></button>
            <button
                @:click="colorButtonClick('red')"
                :class="colorButtonClass('red')"
            ></button>
            <button
                @:click="colorButtonClick('black')"
                :class="colorButtonClass('black')"
            ></button>
            <button
                @:click="colorButtonClick('white')"
                :class="colorButtonClass('white')"
            ></button>
        </div>
    </div>
</template>

<style scoped>
h1 {
    margin-top: 2rem;
    color: #02001f;
    margin-bottom: 1rem;
}

.guess-button,
.secret-container > button,
.rest-hint-container > button {
    background-color: #02001f;
    outline: none;
    border: none;
    padding: 1rem 3.5rem;
    border-radius: 0.5rem;
}

.guess-button {
    padding: 1rem 2rem;
}
.guess-container {
    margin-top: auto;
    display: flex;
    justify-content: space-between;
}

.colors-container {
    display: flex;
    justify-content: space-between;
    margin-top: 1rem;
}
.secret-container {
    display: flex;
    margin-top: 1rem;
    justify-content: space-between;
}

button.color-circle {
    border: none;
    outline: none;
}

button.color-circle.white {
    border: #787878 solid 1px;
    outline: none;
}
button.color-circle.selected {
    border: 2px solid white;
    outline: 2px solid gray;
}
.color-circle {
    width: 3rem;
    height: 3rem;
    border-radius: 50%;
}

.container {
    flex: 1;
    flex-basis: 33.33%;
    max-width: 33vw;
    height: 100%;
    padding: 3rem 2rem;
    display: flex;
    flex-direction: column;
}

.rest-hint-container {
    width: 100%;
    display: flex;
    justify-content: space-between;
}
</style>

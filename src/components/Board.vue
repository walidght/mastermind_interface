<script setup>
import { ref } from 'vue';

const props = defineProps({
    guesses: Array,
    feedbacks: Array,
    minGuesses: Number,
});
</script>

<template>
    <div class="container">
        <div v-for="(code, index) in props.guesses" class="row">
            <div class="code">
                <div
                    v-for="(color, index) in code"
                    :class="`circle ${color}`"
                    :key="index"
                ></div>
            </div>
            <div class="feedback">
                <div
                    v-for="feedbackColor in props.feedbacks[index]"
                    :class="`circle ${feedbackColor}`"
                    :key="index"
                ></div>
                <div
                    v-for="feedbackColor in 4 - props.feedbacks[index]?.length"
                    class="circle gray"
                ></div>
            </div>
        </div>
        <div v-for="el in 10 - props.guesses.length" class="row">
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
        <div class="header">
            <h1>You</h1>
            <p>possible win in {{ props.minGuesses  }} tries w/knuth</p>
        </div>
    </div>
</template>

<style scoped>
.container > div > h1 {
    color: #02001f;
    margin-left: 3rem;
    margin-bottom: 1rem;
}
.container > div > P {
    color: #02001f;
    font-size: 0.7rem;
    margin-bottom: 1rem;
}

.container .header {
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.container {
    height: 100%;
    padding: 3rem 2rem;
    flex: 1;
    display: flex;
    flex-direction: column-reverse;
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

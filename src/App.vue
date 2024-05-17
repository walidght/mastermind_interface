<script setup>
import { ref } from 'vue';
import HelloWorld from './components/HelloWorld.vue';
import Controls from './components/Controls.vue';
import Board from './components/Board.vue';
import AIBoard from './components/AIBoard.vue';

const guesses = ref([]);
const feedbacks = ref([]);
const minGuesses = ref(5);
const AILevel = ref('none');
const turns = ref(0);
const showAI = ref(false);

const updateAILevel = (choice) => {
    AILevel.value = choice;
};
const updateTurns = () => {
    turns.value += 1;
};

const updateMinGuesses = (newVal) => {
    minGuesses.value = newVal;
};

const updateShowAI = (newVal) => {
    console.log('updateShowAI', newVal);
    showAI.value = newVal;
};
</script>

<template>
    <main>
        <div class="header">
            <h1 id="logo">MasterMind Game</h1>
        </div>
        <div class="container">
            <Suspense>
                <Controls
                    :guesses="guesses"
                    :feedbacks="feedbacks"
                    :turns="turns"
                    :level="AILevel"
                    :updateAILevel="updateAILevel"
                    :updateTurns="updateTurns"
                    :updateShowAI="updateShowAI"
                    :updateMinGuesses="updateMinGuesses"
                />
            </Suspense>
            <Board
                :guesses="guesses"
                :feedbacks="feedbacks"
                :minGuesses="minGuesses"
            />
            <Suspense>
                <AIBoard :level="AILevel" :turns="turns" :showAI="showAI" />
            </Suspense>
        </div>
    </main>
</template>

<style scoped>
main {
    display: flex;
    flex-direction: column;
    height: 100vh;
}
.header {
    background-color: #02001f;
    width: 100%;
    padding: 1.2rem 2rem;
}

.container {
    flex: 1;
    display: flex;
    justify-content: space-between;
}
</style>

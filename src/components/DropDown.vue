<template>
    <div class="dropdown">
        <div class="drop-container">
            <p>{{ props.level }}</p>
            <button @click="toggleDropdown" class="dropdown-toggle">V</button>
        </div>

        <ul v-if="isOpen" class="dropdown-menu">
            <li>
                <button @click="optionClicked('none')">No AI</button>
            </li>
            <li>
                <button @click="optionClicked('easy')">Easy</button>
            </li>
            <li>
                <button @click="optionClicked('normal')">Normal</button>
            </li>
            <li>
                <button @click="optionClicked('expert')">Expert</button>
            </li>
            <li>
                <button @click="optionClicked('knuth')">Knuth</button>
            </li>
        </ul>
    </div>
</template>

<script setup>
import { ref } from 'vue';

const props = defineProps({
    level: String,
    updateAILevel: Function,
});

const isOpen = ref(false);

const toggleDropdown = () => {
    isOpen.value = !isOpen.value;
};

const optionClicked = (choice) => {
    isOpen.value = false;
    props.updateAILevel(choice);
};
</script>

<style scoped>
.drop-container {
    display: flex;
    justify-content: space-between;
    align-items: center;
    border: 3px solid #02001f;
    border-radius: 0.5rem;
}

p {
    padding-left: 1rem;
    color: #02001f;
}
.dropdown {
    position: relative;
    display: inline-block;
    width: 100%;
}

.dropdown-toggle {
    cursor: pointer;
    background-color: #02001f;
    outline: none;
    border: none;
    padding: 1rem 1rem;
}

.dropdown-menu li button {
    background-color: white;
    cursor: pointer;
    padding: 0.7rem 0;
    width: 10rem;
    border: none;
    color: #02001f;
    outline: none;
}

.dropdown-menu li:hover button,
.dropdown-menu li:hover {
    background-color: #f1f1f1;
}

.dropdown-menu.show {
    display: block;
}

.dropdown-menu {
    position: absolute;
    top: 100%;
    left: 0;
    background-color: #fff;
    border: 1px solid #ced4da;
    padding: 8px 0;
    list-style: none;
    margin: 0;
}
</style>

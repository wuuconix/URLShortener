<template>
    <div class="box">
        <template v-if="visible">
            <input v-model="origin" type="url" placeholder="PASTE URL, SHORTEN & SHARE" id="origin">
            <div class="custom-container">
                <span>/</span>
                <input v-model="short" type="url" placeholder="CUSTOM URL" id="short">
                <button @click="shorten">SHORTEN</button>
            </div>
            <div class="error" v-if="error">
                <h5>{{ errorMessage }}</h5>
            </div>
        </template>
        <template v-if="!visible">
            <h2>YOUR SHORTENED LINK:</h2>
            <div class="link">
                <input v-model="result" type="url" id="short">
                <button @click="copy">COPY</button>
            </div>
        </template>
    </div>
    <div class="copyright">
        <p>© 2022 Custom URL Shortener</p>
    </div>
</template>

<script setup>
import { ref } from "vue"

const visible = ref(true)
const origin = ref("")
const short = ref("")
const result = ref("")
const error = ref(false)
const errorMessage = ref("")

async function shorten() {
    const response = await fetch("https://conix.tk",
        { method: "POST", body: JSON.stringify({ origin: origin.value, short: short.value }) }
    )
    if (response.status == 200) {
        result.value = `https://conix.tk/${short.value}`
        visible.value = false
    } else if (response.status == 501) {
        error.value = true
        errorMessage.value = `Oh no, The keyword "${short.value}" already exists`
    } else {
        error.value = true
        errorMessage.value = await response.text()
    }
}

async function copy() {
    await navigator.clipboard.writeText(result.value)
}
</script>
<style>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: Montserrat, sans-serif;
}
html, body, #app {
    height: 100%;
}
#app {
    background-color: #007bff;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    animation: color 1s ease-in-out;
}
@keyframes color {
    0% {
        background-color: white;
    }
    30% {
        background-color: white;
    }
    100% {
        background-color: #007bff;
    }
}
</style>
<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@700&display=swap');

.box {
    width: 620px;
    background-color: white;
    margin-top: -10em;
    padding: 50px;
    border-radius: 5px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: flex-start;
}
input {
    background-color: #ebebeb;
    outline: none;
    padding: .5em;
    height: 3em;
    border: none;
    margin-bottom: 10px;
}
input#origin {
    height: 3em;
    font-size: 1em;
    width: 100%;
    border-radius: 6px;
}
input#short {
    height: 2.4em;
    font-size: .8em;
    padding-left: .4em;
    width: 75%;
    border-radius: 6px 0 0 6px;
}
.custom-container, .link {
    width: 100%;
}
.custom-container span {
    height: 2.4em;
    line-height: 2.4em;
    width: 6px;
    margin-right: 4px;
}
button {
    background-color: #007bff;
    height: 2.4em;
    color: white;
    font-size: .8em;
    width: calc(25% - 11px);
    align-self: flex-end;
    border-radius: 0 5px 5px 0;
    border: none;
}
button:hover {
    background-color: #006cf0;
    cursor: pointer;
}
.copyright {
    margin-top: 10px;
    color: white;
}
h2 {
    margin-bottom: 24px;
}
h5 {
    color: #F44336;
}
</style>
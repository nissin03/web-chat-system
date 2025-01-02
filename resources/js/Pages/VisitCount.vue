<script setup>
import {ref} from "vue";
const messages = ref([]);
const message = ref("");


Echo.private("messages").listen("MessageReceived", (e)=> {
    if(!messages.value.find(m => m.id === e.id)) {
        messages.value.push(e);
    }
}); 

function handleSubmit() {
    const msg = {
        id: crypto.randomUUID(),
        message: message.value,
    };
    messages.value.push({
        ...msg,
        who: "Me",
    });
    axios.post("/messages", msg);
}
</script>

<template>
    <div class="container">
        <div id="app" class="chat-container">
            <ul class="chat-window">
                <li v-for="message in messages" :key="message.id">
                    {{ message.message }} - {{ message.who }}
                </li>
            </ul>
            <form class="chat-input" action="" @submit.prevent="handleSubmit">
                <textarea v-model="message" placeholder="Type a message..."></textarea>
                <button type="submit">Send Message</button>
            </form>
        </div>
    </div>
</template>



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
    <div class="container mx-auto max-w-4xl h-screen py-6 px-4">
        <div class="bg-white rounded-lg shadow-lg h-full flex flex-col">
            <!-- Chat Header -->
            <div class="px-6 py-4 border-b border-gray-200">
                <h2 class="text-xl font-semibold text-gray-800">Chat Messages</h2>
            </div>

            <!-- Messages Container -->
            <div class="flex-1 overflow-y-auto p-4 space-y-4">
                <div v-for="message in messages" 
                     :key="message.id"
                     :class="[
                         'flex',
                         message.who === 'Me' ? 'justify-end' : 'justify-start'
                     ]">
                    <div :class="[
                        'max-w-[70%] rounded-2xl px-4 py-2',
                        message.who === 'Me' 
                            ? 'bg-blue-500 text-white rounded-br-none' 
                            : 'bg-gray-100 text-gray-800 rounded-bl-none'
                    ]">
                        <p class="text-sm">{{ message.message }}</p>
                        <div class="text-xs mt-1" 
                             :class="message.who === 'Me' ? 'text-blue-100' : 'text-gray-500'">
                            {{ message.who }}
                        </div>
                    </div>
                </div>
            </div>

            <!-- Input Form -->
            <div class="border-t border-gray-200 p-4 bg-gray-50">
                <form @submit.prevent="handleSubmit" class="flex gap-2">
                    <textarea 
                        v-model="message" 
                        placeholder="Type a message..." 
                        class="flex-1 resize-none rounded-lg border border-gray-300 focus:border-blue-500 focus:ring-1 focus:ring-blue-500 min-h-[44px] max-h-32 p-2 text-sm"
                        rows="1"
                    ></textarea>
                    <button 
                        type="submit" 
                        class="px-4 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600 transition-colors duration-200 flex items-center justify-center"
                    >
                        Send
                    </button>
                </form>
            </div>
        </div>
    </div>
</template>
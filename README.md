# Vue AI Chatbot App

📌 **Overview**

This project is a simple ChatGPT-like chatbot application built with **Vue.js** and the **Mistral AI API**. Users can input messages, receive AI-generated responses, and interact with the chatbot in real-time. The goal of this tutorial is to help beginners with experience in development build a Vue.js app from start to finish.

---

🎯 **Features**

- 💬 **Chat with AI** using the Mistral API
- 🎨 **Responsive UI** with Vue & Tailwind CSS
- ⚡ **Reactive state management** using Vue Composition API
- 🌐 **API integration** with Axios for smooth requests

---

🛠️ **Tech Stack**

- **Vue 3** with Composition API
- **Vite** for fast development setup
- **Tailwind CSS** for styling
- **Axios** for API requests

---

🚀 **Getting Started**

### 1. Prerequisites

Before you begin, make sure you have the following installed:

- **Node.js** (LTS version recommended): [Download Node.js](https://nodejs.org/)

**Get a free API key from Mistral AI:**

1. Create an organization on Mistral AI's platform.
2. Subscribe to the **Experiment plan** (free) from the subscriptions section in your account settings.
3. Generate an **API key** by going to the **API Keys** section and clicking **Create new key**.
4. Name it something like `Vue Tech Challenge 2025` and save it for use in the next steps.

---

### 2. Create Your Project Using Vite

1. Clone the repository:

   ```bash
   git clone https://github.com/YOUR_USERNAME_HERE/vue-tech-challenge-ai-chatbot.git
   
2. Initialize a new Vite project in the current directory with Vue:
    
    ```bash
    npm create vite@latest . --template vue
    ```
    Select to **ignore files and continue** in project. When prompted to *Select a Framework*, select **Vue**. We'll also be using **Typescript** variant.

3. Install the project dependencies:

    ```bash
    npm install

### 3. Create an .env File

1. Create a .env file in the root of the project directory:
    
        touch .env
    
2. Inside the .env file, add your Mistral API key:
    
    ```
    VITE_MISTRAL_API_KEY=your_api_key_here
    ```
    > **_NOTE:_**  Environment variables must start with VITE prefix. More on that [here](https://vite.dev/guide/env-and-mode).

    
### 4. Run the App
Start the development server:

    npm run dev

For Vite, this will usually be available at http://localhost:5173. Feel free to delete the `HelloWorld.vue`.


### 5. Create the Chat Component
In Vue, components are the building blocks of your app. They allow you to create reusable, self-contained units of functionality. In this step, we'll create the chat component, where users can type messages and interact with the chatbot.

#### Step 5.1: Create a New Vue Component File
1. Create a new file `Chat.vue` inside the `src/components/` directory.
Note: This is similar to creating a new component in **React** (Chat.js) or **Angular** (chat.component.ts).

#### Step 5.2: Define the Template (HTML Structure)
Inside the `Chat.vue` file, start by defining the HTML structure of your chat app:
```
<template>
  <div class="chat-container">
    <div class="messages" v-for="message in messages" :key="message.id">
      <p :class="message.type">{{ message.text }}</p>
    </div>
    <input v-model="userInput" @keyup.enter="sendMessage" placeholder="Type your message..." />
  </div>
</template>

```

- The `<template>` section is where we define the HTML structure of the component.
- `v-for="message in messages"` is a Vue directive that loops through the `messages` array and displays each message.
- The `v-bind:class="message.type"` or shorthand `:class="message.type"` binds a class dynamically to each message based on its type (`user` or `bot`).
- The `v-model="userInput"` binds the input field value to the `userInput` data property, so the component's state is automatically updated when the user types.
- `@keyup.enter="sendMessage"` listens for the Enter key press to trigger the sendMessage function.

#### Step 5.3: Define the Script (Component Logic)
Next, let's define the logic behind the chat functionality. In Vue, the `<script>` section holds the JavaScript logic for your component.
```
...

<script setup>
import { ref } from 'vue';

const userInput = ref('');
const messages = ref([]);

const sendMessage = async () => {
  if (!userInput.value) return;

  messages.value.push({ text: userInput.value, type: 'user' });
  
  try {
    const apiKey = import.meta.env.VITE_MISTRAL_API_KEY;
    if (!apiKey) {
      throw new Error('API key is not set');
    }

    const response = await fetch('https://api.mistral.ai/v1/chat/completions', {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${apiKey.trim()}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        model: "mistral-tiny",
        messages: [{ role: 'user', content: userInput.value }]
      })
    });

    const data = await response.json();
    
    if (!response.ok) {
      throw new Error(`API Error: ${data.error?.message || response.statusText}`);
    }

    messages.value.push({ 
      text: data.choices[0].message.content, 
      type: 'bot' 
    });
  } catch (error) {
    console.error('API Error:', error);
    messages.value.push({ 
      text: `Error: ${error.message}`, 
      type: 'bot' 
    });
  }

  userInput.value = '';
};
</script>
```

- `const userInput = ref('')` and `const messages = ref([])` are Vue reactive references (similar to **React's useState**).

In **React**, you would use useState to store the input value and messages:
```
const [userInput, setUserInput] = useState('');
const [messages, setMessages] = useState([]);
```
In **Angular**, you'd define the state in the component class:
```
export class ChatComponent {
  userInput: string = '';
  messages: { text: string; type: string }[] = [];
}
```
- `sendMessage` is an asynchronous function that handles the API call to the Mistral API. It adds messages to the messages array, which automatically updates the UI because of Vue’s reactivity system.
- The API call is made using fetch, where we send a POST request to the Mistral API's chat completions endpoint.

#### Step 5.4: Define the Styles (CSS)
Next, we will style our chat component. The `<style>` section in Vue allows you to write scoped CSS that only applies to the current component.
```
...

<style>
.chat-container {
  max-width: 600px;
  margin: auto;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 8px;
}
.messages p {
  padding: 10px;
  margin: 5px 0;
  border-radius: 5px;
}
.user {
  background: #d1e7ff;
  text-align: right;
}
.bot {
  background: #f1f1f1;
  text-align: left;
}
input {
  width: 100%;
  padding: 12px;
  margin-top: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 16px;
  box-sizing: border-box;
}
input:focus {
  outline: none;
  border-color: #4a90e2;
  box-shadow: 0 0 0 2px rgba(74, 144, 226, 0.2);
}
</style>

```
- We’ve defined basic styles for the chat container, message bubbles, and user/bot messages.
- In **React**, you would typically use **CSS Modules** or **styled-components** for scoped styling.
- In **Angular**, you would use styleUrls to link to an external stylesheet, or you could use inline styles directly within the component.

#### Step 5.5: Make the Component Reactive
One of the great features of Vue is its reactivity system. The state of the app, such as `messages` and `userInput`, automatically triggers updates to the UI whenever the data changes.
- In **React**, you'd update the state using `setMessages` or `setUserInput`.
- In **Angular**, you'd update the component's properties, and Angular’s change detection would automatically reflect the updates in the view.


### 6. Integrate the Chat Component
Next, open `src/App.vue` and replace its contents with:
```
<template>
  <div>
    <h1 class="text-center text-2xl font-bold">Vue Mistral AI Chatbot</h1>
    <Chat />
  </div>
</template>

<script setup>
import Chat from './components/Chat.vue';
</script>
```

### 7. Test the Application
Once everything is set up, start the app again by running:

```
npm run dev
```

## 🎉 Congratulations on Completing the AI Chatbot Tech Challenge!  

Great job on building your AI-powered chatbot using Vue! Feel free to play around with the styling to customize the UI they way you see fit. 🚀 We hope this challenge helped you gain hands-on experience with Vue.js and AI integration.  

### ✅ Next Steps:  
1. **Mark the course as completed** in **LMS 365**.  
2. **Ping Garrett Levy on Teams** to confirm your completion.  

### 🚀 Hosting Your Chatbot  
If you’d like to deploy your chatbot and share it, here are some recommended hosting options:  

- **Vercel**
- **Netlify**
- **Firebase Hosting** 

If you need help with deployment, feel free to reach out. **Congrats again, and happy coding!** 🎉  

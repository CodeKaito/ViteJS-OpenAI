# Building and Deploying a ChatGPT AI App in JavaScript using ViteJS

### Introduction

This is a step-by-step guide on how to build and deploy a ChatGPT AI App in JavaScript using ViteJS. ViteJS is a lightning-fast frontend build tool that is designed to make the development process easier and faster. Using ViteJS, we can quickly build and deploy web apps with minimum configurations. 

ChatGPT is an AI-based conversational model that can respond to natural language queries or texts. It is based on the GPT (Generative Pre-trained Transformer) model, which is a state-of-the-art deep learning model that can generate human-like texts. In this tutorial, we will be using the OpenAI GPT-3 API to build our ChatGPT AI App.

### Prerequisites

Before we proceed, ensure you have the following:

- Basic knowledge of JavaScript programming language
- OpenAI API credentials 
- A working internet connection

### Step 1: Setup

Start by creating a new project folder and initializing the project with NPM.

```
mkdir chatgpt-app
cd chatgpt-app
npm init -y
```

### Step 2: Install Dependencies

Next, we need to install the necessary dependencies for our app. We will be using the following dependencies:

- ViteJS for building our app
- Axios for sending requests to the OpenAI API

Install the dependencies using the following command:

```
npm install vite axios
```

### Step 3: Create the App

Now that we have the dependencies installed let's create the app. 

Create a new file `index.html` and add the following code:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/favicon.ico" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>ChatGPT.it</title>
    <link rel="stylesheet" href="style.css">
  </head>
  <body>
    <div id="app">
      <div id="chat_container"></div>

      <form>
        <textarea name="prompt" rows="1" cols="1" placeholder="Send a message"></textarea>
        <button type="submit"><img src="assets/send.svg" alt=""></button>
      </form>
    </div>

    <script type="module" src="/script.js"></script>
  </body>
</html>

```

This file will act as the entry point of our app. We created a `div` element with an `id` of `app` where we will render our app, and we also referenced our JavaScript file `main.js`.

Create a new folder `src` in the root directory of our project, and then create a new file `main.js` inside the `src` folder. Add the following code to the `main.js` file:

```javascript
import './style.css'

import bot from './assets/bot.svg'
import user from './assets/user.svg'

const form = document.querySelector('form')
const chatContainer = document.querySelector('#chat_container')

let loadInterval

function loader(element) {
  element.textContent = '';

  loadInterval = setInterval(() => {
    // Update the text content of the loading indicator
    element.textContent += '.';

    // If the loading indicator has reached three dots, reset it
    if (element.textContent === '....') {
      element.textContent = '';
  }
}, 300);
}

function typeText(element, text) {
  let index = 0;

  let interval = setInterval(() => {
    if (index < text.length) {
      element.innerHTML += text.charAt(index);
      index++;
    } else {
      clearInterval(interval)
    }
  }, 20)
}

// generate unique ID for each message div of bot
// necessary for typing text effect for that specific reply
// without unique ID, typing text will work on every element
function generateUniqueId() {
  const timestamp = Date.now();
  const randomNumber = Math.random();
  const hexadecimalString = randomNumber.toString(16);

  return `id-${timestamp}-${hexadecimalString}`;
}

function chatStripe(isAi, value, uniqueId) {
  return (
      `
      <div class="wrapper ${isAi && 'ai'}">
          <div class="chat">
              <div class="profile">
                  <img 
                    src=${isAi ? bot : user} 
                    alt="${isAi ? 'bot' : 'user'}" 
                  />
              </div>
              <div class="message" id=${uniqueId}>${value}</div>
          </div>
      </div>
  `
  )
}

const handleSubmit = async (e) => {
  e.preventDefault()

  const data = new FormData(form)

  // user's chatstripe
  chatContainer.innerHTML += chatStripe(false, data.get('prompt'))

  // to clear the textarea input
  form.reset()

  // bot's chatstripe
  const uniqueId = generateUniqueId()
  chatContainer.innerHTML += chatStripe(true, " ", uniqueId)

  // to focus scroll to the bottom 
  chatContainer.scrollTop = chatContainer.scrollHeight;

  // specific message div 
  const messageDiv = document.getElementById(uniqueId)

  // messageDiv.innerHTML = "..."
  loader(messageDiv);

  const response = await fetch('http://localhost:5000', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
        },
        body: JSON.stringify({
            prompt: data.get('prompt')
        })
    })

    clearInterval(loadInterval);
    messageDiv.innerHTML = " ";

    if (response.ok) {
        const data = await response.json();
        const parsedData = data.bot.trim() // trims any trailing spaces/'\n' 

        typeText(messageDiv, parsedData)
    } else {
        const err = await response.text()

        messageDiv.innerHTML = "Something went wrong";
        alert(err);
    }
}

form.addEventListener('submit', handleSubmit)
form.addEventListener('keyup', (e) => {
    if (e.keyCode === 13) {
        handleSubmit(e)
    }
})
```

Here, we imported the axios library to make API requests to the OpenAI API. We defined an `api_key` variable and set it to your OpenAI API key. We defined a `prompt` variable as the initial message that the user will send. Then we created two functions `generateResponse` and `chat`. The `generateResponse` function sends a POST request to the OpenAI API with the `prompt` as the input and the AI generates a response based on the input. The response is then returned and stored in the `response` variable.

The `chat` function gets the `chatContainer` element using `getElementById` and then appends the user's input and the AI's response to the element.

### Step 4: Build and Run the App

Now that we have created the app, let's build and run it. 

In the terminal, run the following command to start the development server:

```
npx vite
```

This will start the development server at `http://localhost:3000/`. You should see the message "Hello, how are you?" and the response generated by the AI in the browser.

### 5. Deploy to production.

To deploy your app to production, you need to create a production build using the following command:

```
npx vite build
```
This will create a `dist` directory that contains the optimized production version of your app. You can then deploy the contents of the `dist` directory to your web server or hosting service of choice.

### Conclusion

In this tutorial, we've seen how to build and deploy a ChatGPT AI App in JavaScript using ViteJS. We used the OpenAI GPT-3 API to generate responses, and we used ViteJS to quickly build our app. 

With this knowledge, you can now build and deploy similar AI-based apps using the OpenAI API and JavaScript frameworks like ViteJS.

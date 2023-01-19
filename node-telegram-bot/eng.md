# How to create a Telegram Bot using NodeJS

In this tutorial, we will create a simple bot that will send a random joke from a list of jokes and a random cat image to your chat.

## Prerequisites

You should have NodeJS installed for this tutorial. If you don't have it, you can download it from [here](https://nodejs.org/en/download/).

## Creating the bot

First, you need to create a bot. Telegram make it really easy, and all you need to do is to talk to the BotFather. Send a message, and then follow the instructions. You can find the BotFather [here](https://t.me/botfather).

When you open it, click on the start button, and then send the `/newbot` command. Follow the instructions, and you will get a **token** for your bot. Save it, you will need it later.

## Creating the NodeJS project

Create a new folder for your project, and open it in the terminal.

To create a NodeJS project, you can use the `npm init` command. This will create a `package.json` file, that will contain all the information about your project. You can also use the `-y` flag to skip the questions.

```bash
npm init -y
```

## Installing the dependencies

In this project we will use the [Telegraf](https://telegraf.js.org/) library to create the bot. To install it, you can use the `npm install` command.

```bash
npm install telegraf
```

This will install the library and all the dependencies.
There are other libraries that you can use to create a bot, but I prefer Telegraf because it is really easy to use.

We also will use the folowing libraries:

- dotenv to load the environment variables
- axios: to make HTTP requests
- express: to create the server

We can install all of them using the `npm install` command.

```bash
npm install dotenv axios express
```

If you do not know any of those libraries, that is okay and we are going very light on them. You can read more about them in the documentation.

## Creating the server

First, we need to create a server to run our bot. We will use the express library to create it.
In the root of your project, create a new file called `index.js` and add the following code:

```js
const express = require("express");
const app = express();

app.use(express.json());

app.get("/", (req, res) => {
  res.send("Hello World!");
});

app.listen(3000, () => {
  console.log("Server running on port 3000");
});
```

This will create a server that will listen on port 3000. You can change it if you want.

To run the server, you can use the `node` command.

```bash
node index.js
```

Or you can use the `nodemon` library to run it. This will restart the server every time you make a change.

```bash
npm install -g nodemon
nodemon
```

If you open your browser and go to `http://localhost:3000`, you should see the `Hello World!` message. If you don't, check the console for errors.

This endpoint will be used only for this test, so you can remove it later.

## Creating the bot

At the root of your project, create a new file called `.env` and add the following

```bash
BOT_TOKEN=your_token
```

Add your token that you got from BotFather.

Import the `dotenv` library and Telegraf at the top of your `index.js`.

```js
const { Telegraf } = require("telegraf");

require("dotenv").config();
```

Then, create a new instance of the class, passing the token as a parameter.

```js
const bot = new Telegraf(process.env.BOT_TOKEN);
```

This part can be a bit confusing, so let's break it down.

### Ngrok

We need to create a Webhook to receive the messages from Telegram.

First, install Ngrok. You can find the instructions [here](https://ngrok.com/download).

Then, run it with the following command:

```bash
ngrok http 3000
```

This will create a tunnel to your local server. It should be running on the same port that your node server is running. You should see something like this on the console:

IMAGE

Copy the URL that is in the `Forwarding` section. On the index create a const called `WEBHOOK_URL` and add the URL.

```js
const WEBHOOK_URL = "https://your_url";
```

### Setting the Webhook

Telegraf does have a method to set the Webhook, but I found it easier to set it using a request. So, we will use the `setWebhook` method from Telegram API.
To use it, first you should create const with the link to the API, token and URL of the Webhook.

Import the `axios` library at the top of your `index.js`.

```js
const axios = require("axios");
```

Create a const called `TELEGRAM_API` and add the following code:

```js
const TELEGRAM_API = `https://api.telegram.org/bot${process.env.BOT_TOKEN}`;
```

And create a function to set the Webhook that makes a post to the setWebhook endpoint.

```js
const setWebhook = async () => {
  try {
    const response = await axios.post(
      `${TELEGRAM_API}/setWebhook?url=${WEBHOOK_URL}`
    );
    console.log(response.data);
  } catch (error) {
    console.log(error);
  }
};
```

Call the function to set the Webhook inside the `app.listen` function.

```js
app.listen(3000, async () => {
  console.log("Server running on port 3000");
  await setWebhook();
});
```

This function will make a POST request to the API, passing the URL of the Webhook. If everything goes well, you should see the following message on the console:

```js
{ ok: true, result: true, description: 'Webhook was set' }
```

## Starting the bot

To start the bot, you can use the `bot.launch` method.

```js
bot.launch();
```

This will start the bot and listen for messages.

Your `index.js` should look like this:

```js
const express = require("express");
const axios = require("axios");
const { Telegraf } = require("telegraf");

require("dotenv").config();

const app = express();

app.use(express.json());

const bot = new Telegraf(process.env.BOT_TOKEN);

const WEBHOOK_URL = "https://dbaf-192-144-124-11.sa.ngrok.io";
const TELEGRAM_API = `https://api.telegram.org/bot${process.env.BOT_TOKEN}`;

const setWebhook = async () => {
  try {
    const response = await axios.post(
      `${TELEGRAM_API}/setWebhook?url=${WEBHOOK_URL}`
    );
    console.log(response.data);
  } catch (error) {
    console.log(error);
  }
};

bot.launch();

app.get("/", (req, res) => {
  res.send("Hello World!");
});

app.listen(3000, async () => {
  console.log("Server running on port 3000");
  await setWebhook();
});
```

## Sending a message

We can set a command that when you type `/start` the bot will send a message.

```js
bot.start((ctx) => ctx.reply("Hello World!"));
```

If you go to Telegram and type `/start`, you should see the message.

## Random Joke

To send a random joke, we will use the [Official Joke API](https://official-joke-api.appspot.com/random_joke). It is a free API that returns a random joke. To use it, we need to make a GET request to the API and get the joke from the response.

```js
bot.command("joke", async (ctx) => {
  try {
    const response = await axios.get(
      "https://official-joke-api.appspot.com/random_joke"
    );
    const { setup, punchline } = response.data;
    const message = `${setup}\n${punchline}`;
    ctx.reply(message);
  } catch (error) {
    console.log(error);
  }
});
```

If you go to Telegram and type `/joke`, you should see a random joke.

## Sending a photo

To send a photo, we will use the [Cat API](https://thecatapi.com/). It is a free API that returns a random cat image. To use it, we need to make a GET request to the API and get the image from the response.

```js
bot.command("cat", async (ctx) => {
  try {
    const response = await axios.get(
      "https://api.thecatapi.com/v1/images/search"
    );
    const { url } = response.data[0];
    ctx.replyWithPhoto(url);
  } catch (error) {
    console.log(error);
  }
});
```

Same trick, if you send a message to your bot with `/cat`, you should see a random cat image.

## Conclusion

Congratulations! You have created your first Telegram bot.
You can find the code on [GitHub](https://github.com/debora-rebelatto/node-telegram-bot).

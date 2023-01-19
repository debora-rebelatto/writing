# Como criar um Bot do Telegram com NodeJs

Neste tutorial, você aprenderá como criar um bot do Telegram com NodeJs que envia uma piada aleatória e uma foto de gato no chat.

## Pré-requisitos

Você deve ter o NodeJs instalado para este tutorial. Se você não tiver, você pode baixá-lo [aqui](https://nodejs.org/en/download/).

## Criando o bot

Primeiro, você precisa criar um bot. O Telegram facilita muito, e tudo que você precisa fazer é conversar com o BotFather. Envie uma mensagem e siga as instruções. Você pode encontrá-lo [aqui](https://t.me/botfather).

Quando você abrir, clique no botão de início e envie o comando `/newbot`. Siga as instruções e você receberá um **token** para o seu bot. Salve-o, você precisará dele mais tarde.

## Criando o projeto NodeJs

Crie uma nova pasta para o seu projeto e abra-a no terminal.

Para criar um projeto NodeJs, você pode usar o comando `npm init`. Isso criará um arquivo `package.json` que conterá todas as informações sobre o seu projeto. Você também pode usar a flag `-y` para pular as perguntas.

```bash
npm init -y
```

## Instalando as dependências

Neste projeto, usaremos a biblioteca [Telegraf](https://telegraf.js.org/) para criar o bot. Para instalá-lo, você pode usar o comando `npm install`.

```bash
npm install telegraf
```

Isso instalará a biblioteca e todas as dependências.
Existem outras bibliotecas que você pode usar para criar um bot, mas prefiro o Telegraf porque é muito fácil de usar.

Também usaremos as seguintes bibliotecas:

- dotenv para carregar as variáveis de ambiente
- axios: para fazer solicitações HTTP
- express: para criar o servidor

Podemos instalar todas elas usando o comando `npm install`.

```bash
npm install dotenv axios express
```

Se você não conhece nenhuma dessas bibliotecas, tudo bem, não estamos nos aprofundando nelas. Você pode ler mais sobre elas na documentação.

## Criando o server

Para criar o servidor, crie um novo arquivo chamado `index.js` e adicione o seguinte código:

```js
const express = require("express");
const app = express();

app.use(express.json());

app.get("/", (req, res) => {
  res.send("Hello World!");
});

app.listen(3000, () => {
  console.log("Server is running on port 3000");
});
```

Isso vai criar um servidor simples que responde a uma solicitação GET no caminho `/` com a mensagem "Hello World!".

Para inicializar o servidor, você pode usar o comando `node`:

```bash
node index.js
```

Ou você pode usar a biblioteca [nodemon](https://www.npmjs.com/package/nodemon) para reiniciar o servidor automaticamente quando você fizer alterações no código.

```bash
npm install -g nodemon
```

```bash
nodemon
```

Se você abrir o navegador e digitar `http://localhost:3000`, verá a mensagem "Hello World!". Se não funcionar, verifique se o servidor está sendo executado.

Esse endpoint apenas é usado para teste, então você pode remove-lo depois.

## Criando o bot

Na raíz do projeto, crie um novo arquivo chamado `.env` e adicione o seguinte:

```bash
BOT_TOKEN=your_token
```

E adicione o token do seu bot.

Importe o `dotenv` e o `Telegraf` no arquivo `index.js` adicionando o seguinte código no topo do arquivo:

```js
const { Telegraf } = require("telegraf");
require("dotenv").config();
```

Agora, crie uma nova instância do Telegraf e passe o token do seu bot como parâmetro:

```js
const bot = new Telegraf(process.env.BOT_TOKEN);
```

## Ngrok

Precisamos criar um Webhook para receber as mensagens do Telegram.

Primeiro, instale o Ngrok. Você pode baixá-lo [aqui](https://ngrok.com/download).

No terminal, execute o seguinte comando:

```bash
ngrok http 3000
```

Isso criará um túnel para o seu servidor. Você verá algo como isso:

Copie a URL que está em `Forwarding`. No `index.js`, adicione o seguinte código:

```js
const WEBHOOK_URL = "your_url";
```

## Criando o Webhook

O Telegraf tem um método para setar o Webhook, mas eu achei mais fácil usar o axios para fazer a requisição. Então, vamos usar o método `setWebhook` da API do Telegram para obter a URL do Webhook e adicionar o token do bot.

Importamos o axios no topo do arquivo:

```js
const axios = require("axios");
```

Criamos uma constante para armazenar a URL da API do Telegram:

```js
const TELEGRAM_API = `https://api.telegram.org/bot${process.env.BOT_TOKEN}`;
```

E agora, vamos criar uma função para setar o Webhook que faz um post para o endpoint `/setWebhook` da API do Telegram.

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

Chamamos a função dentro do `app.listen`:

```js
app.listen(3000, async () => {
  console.log("Server is running on port 3000");
  await setWebhook();
});
```

Se tudo der certo, você verá a seguinte mensagem no terminal:

```js
Server is running on port 3000
{ ok: true,
  result: true,
  description: 'Webhook was set' }
```

## Recebendo mensagens

Para iniciar o bot, basta chamar o método `launch`:

```js
bot.launch();
```

Isso vai iniciar o bot e fazer com que ele receba as mensagens do Telegram.

Para receber as mensagens, precisamos usar o método `on` do Telegraf. Ele recebe dois parâmetros: o primeiro é o tipo de mensagem que queremos receber e o segundo é uma função que será executada quando a mensagem for recebida.

```js
bot.on("text", (ctx) => {
  console.log(ctx.message);
});
```

Vamos fazer o bot responder com "Olá, {nome}!" quando receber uma mensagem dizendo "Olá"

```js
bot.on("text", (ctx) => {
  if (ctx.message.text === "Olá") {
    ctx.reply(`Olá, ${ctx.message.from.first_name}!`);
  }
});
```

## Enviando mensagens

Para enviar mensagens, usamos o método `reply` do contexto da mensagem.

```js
ctx.reply("Olá, mundo!");
```

## Gerando piada aleatória

Vamos usar a API [Official Joke API](https://official-joke-api.appspot.com/random_joke) para gerar piadas aleatórias. É uma API que retorna uma piada aleatória em JSON. Para usá-la, precisamos fazer um GET request para o endpoint `/random_joke`.

```js
bot.command("piada", async (ctx) => {
  try {
    const response = await axios.get(
      "https://official-joke-api.appspot.com/random_joke"
    );
    const joke = response.data;
    ctx.reply(`${joke.setup}\n${joke.punchline}`);
  } catch (error) {
    console.log(error);
  }
});
```

Se você for no Telegram e digitar `/piada`, o bot vai responder com uma piada aleatória.

## Enviando uma foto

Para enviar uma foto, usamos o método `replyWithPhoto` do contexto da mensagem.

```js
ctx.replyWithPhoto("https://picsum.photos/200/300");
```

Vamos enviar uma foto de galinha aleatória usando a API [Chicken Coop](https://chickencoop.xyz/).

```js
bot.command("galinha", async (ctx) => {
  try {
    const response = await axios.get("https://chickencoop.xyz/api/chicken");
    const chicken = response.data;
    ctx.replyWithPhoto(chicken.image);
  } catch (error) {
    console.log(error);
  }
});
```

## Enviando um GIF

Para enviar um GIF, usamos o método `replyWithAnimation` do contexto da mensagem.

```js
ctx.replyWithAnimation(
  "https://media.giphy.com/media/3o7TKsQ8DmMoyyR0Ww/giphy.gif"
);
```

Vamos enviar um GIF aleatório de cachorro usando a API [Dog API](https://dog.ceo/dog-api/).

```js
bot.command("cachorro", async (ctx) => {
  try {
    const response = await axios.get("https://dog.ceo/api/breeds/image/random");
    const dog = response.data;
    ctx.replyWithAnimation(dog.message);
  } catch (error) {
    console.log(error);
  }
});
```

## Conclusão

Nesse tutorial, aprendemos a criar um bot do Telegram usando o Telegraf e a API do Telegram. Vimos como criar um bot, setar o Webhook e receber e enviar mensagens.

Você pode ver o código completo [aqui](https://github.com/debora-rebelatto/node-telegram-bot).

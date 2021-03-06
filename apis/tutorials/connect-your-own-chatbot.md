---
description: Connect your own chatbots to Tiledesk
---

# Hello World tutorial for external chatbots integration

Tiledesk is designed to allow external chatbots to easy communicate with your Agents or End users. Once a chatbot receive an authentication token from Tiledesk he can easily call many APIs to modify the state of a Request \(the support conversation\) changing Departments, inviting Agents, sending scheduled messages, use the chatbot microlanguage to simplify interaction with buttons, images, messages' timing etc.

This tutorial will show you how to create a very basic chatbot integration, allowing you to reply to specific messages sent by the End user.

## Signup a user on Tiledesk

To use Tiledesk APIs or integrate your own chatbots is mandatory to signup a new user on our **beta** environment. It's available on the following link [https://support-pre.tiledesk.com/dashboard](https://support-pre.tiledesk.com/dashboard)

**The previous APIs end-point will change as soon as the beta version will be released as Tiledesk v2. This tutorial will be updated accordingly.**

After signup please follow the proposed wizard to create your first Tiledesk project \(you can jump the last step, relative to the widget installation\).

As soon as you create the project you will be redirected to the project home.

To integrate an external bot, we'll need a web endpoint where all the chatbot's requests will be forwarded. We'll use the well-known [Repl.it](https://repl.it) service to fast create our own web endpoint.

## Create and configure external chatbot endpoint

Go on the [repl.it](https://repl.it) and press "+ new repl" button. Then select NodeJS as the programming environment and choose a unique name of you repl propject. We use _tiledeskbot_, that obviously you can't use because it was already taken for this tutorial :\)

![](../../.gitbook/assets/image%20%2842%29.png)

Now push on the **examples** link in the generated code. A popup like the following will open. Choose the "Server \(Express\)" option.

![](../../.gitbook/assets/image%20%2898%29.png)

Your source code will change, like the following:

![](../../.gitbook/assets/image%20%2899%29.png)

{% hint style="info" %}
We'll use **NodeJS** for this example, due to his simplicity, low cost hosting and low learning curve. But keep in mind that the concepts in this tutorial can be easily applied to every web framework of your choice.
{% endhint %}

As soon as the _repl_ project is ready you will see something like this:

![](../../.gitbook/assets/image%20%28114%29.png)

Now we can add a new HTTP method POST to our web application, lets call this **/bot.** The new source will look like this:

![](../../.gitbook/assets/image%20%2884%29.png)

We can reach this url using the full address \(with _HTTP POST_ method\):

[https://tiledeskbot.andreasponziell.repl.co/bot](https://tiledeskbot.andreasponziell.repl.co/bot)

This url is the **external bot endpoint**. We'll use this later.

Now open the **Settings** menù on the left panel of our Tiledesk project, selecting the **Bots** option

![](../../.gitbook/assets/image%20%28109%29.png)

Press the ADD BOT button, to create your own external bot. Choose the "External" option.

We must chose a name for the bot and placing in the Url field the **external bot endpoint** url of the repl app:

![](../../.gitbook/assets/image%20%2830%29.png)

Click the CREATE BOT button. Tolobot is now available in our summary list:

![](../../.gitbook/assets/image%20%2896%29.png)

Now it's time to write some code to make our bot service functional.

Go back to the repl project. In the index.js file modify the **/bot** service, copying and pasting the following code:

```text
const express = require('express');
const bodyParser = require('body-parser');
const { TiledeskClient } = 
  require('@tiledesk/tiledesk-chatbot-client');

const app = express();
app.use(bodyParser.json());

app.get('/', (req, res) => {
  res.send('Hello Express app!')
});

app.post('/bot', (req, res) => {
  const tdclient = 
    new TiledeskClient({request: req, response: res});
  console.log("You asked: " + tdclient.text)
  // immediatly reply to TILEDESK
  res.status(200).send({"success":true});

  // messaging is asynch.
  let msg = {
    "text": "Hello from Tiledesk external chatbot!",
    "type": "text",
    "senderFullname": tdclient.botName
  }
  tdclient.sendMessage(msg, function(err, res, resbody) {
      console.log("Message sent.")
  })
})

app.listen(3000, () => {
  console.log('server started');
});
```

To send messages to the current conversation \(and to do other interesting stuff on the same conversation\) we used Tiledesk Chatbot Client library, that we imported on top of the the index.js file:

```text
const { TiledeskClient } = 
  require('@tiledesk/tiledesk-chatbot-client');
```

You can find the full code of this tutorial on the repl linked here:

[https://repl.it/@andreasponziell/tiledeskwelcomebot](https://repl.it/@andreasponziell/tiledeskwelcomebot)

Here you can find an alternative version of the same code using raw calls to Tiledesk REST APIs instead of the NodeJS library.

[https://repl.it/@andreasponziell/tiledeskbot](https://repl.it/@andreasponziell/tiledeskbot)

## Configure a Route for the chatbot

Now that our code is ok, we should configure a routing rule to make this chatbot available to our users. Select the **Routing** option and configure the corresponding rules as follows, activating the Bot, selecting Tolobot and marking the **Bot only** option for this routing, so **Tolobot will be the only available Agent**.

![](../../.gitbook/assets/image%20%2820%29.png)

## Live test

To test our chatbot go to the **Requests** menù and press the green "Simulate visitor" button as shown in the following figure.

![](../../.gitbook/assets/image%20%28107%29.png)

A new browser Tab will open with the widget working as if it is already installed on your website.

Push the **New conversation** button on the widget. A conversation will open on the default routing. A hidden message is sent to your bot, if activated \(as in our example\). Your bot will reply with the message you previously configured in the code:

![](../../.gitbook/assets/image%20%28106%29.png)

In the next tutorial you will learn how to interact with a Dialogflow agent directly from an external chatbot endpoint.

Do you have feedback on this article? Please send us your feedback writing an email to info@tiledesk.com


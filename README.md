# Gist-This

A [Gist](https://gist.github.com/)+[Slack](https://slack.com/) integration using [WebTask.io](https://webtask.io/) as a communication nexus between the two services.

## How does it work?

You can create your own integrations to quickly create gists in Slack channels. When you send a request from Slack to this webtask, it will create an anonymous file in Gist and send the generated URL to a Slack WebHook. 

For example, you could create a _slash command_ in Slack, like this ```/yourcommand <filename.extension> <my-code-snippet>```, and easily share your gists with your team.

## What do I need?

### Slack Integrations

So, first you'll need to add two integrations in Slack: 

1. a [Slash Command](https://api.slack.com/slash-commands) to send the gist content to our webtask. Take note of the ```token``` generated by this integration, you'll use it when creating the webtask.
1. an [Incoming WebHook](https://api.slack.com/incoming-webhooks) that will receive the gist result and post a message in our Slack channel. Take note of the WebHook URL as you'll also use it when creating the webtask.

For example, you can create a Slash command ```/gist-this [filename.ext] [code-snippet]``` and an Incoming WebHook that post messages to the #code channel in Slack.

### Webtask.io

So... What is a webtask? An amazingly fast way to 'run code with an HTTP call' as the [home](https://webtask.io) page says. See the [documentation](https://webtask.io/docs/how) to understand how it works.

But, let's just say that it's a way to expose a Node.js module with a URL. You call this URL and your code is executed. Simple as that.

To create your own **webtask container**, sign-up for free [here](https://webtask.io/cli) and follow the steps to install **Webtask CLI** in your computer.

> **Note:** You need to install [Node.js](https://nodejs.org/) first.

#### Starting gist-this.js

Once webtask is installed and initialized, follow these steps:

1. Our module requires 2 _secrets_: the Slack Token from the slash command to validate incoming requests, and the WebHook URL to post the generated gist URL. Create the webtask using the following command:

	````
	wt create gist-this.js --secret SLACK_TOKEN={command-token} --secret SLACK_URL={webhook-url}
	````
	
1. This command will show you the URL that you can use to invoke the webtask. Copy this URL and update your Slash command in Slack to send a POST message to the webtask.

#### Create gists in Slack

You are good to go. Go to a Slack channel, and use your slash command by typing the filename and the content of gist. For example:

````
/gist-this helloworld.js function hello() { console.log ('Hello World'); }
````

This will create an anonymous Gist with the name **helloworld.js** and the code snippet as its body. You can use multiple lines to create larger gists.

> **Note:** You can use this to create documents (e.g. a markdown file, my-doc.md) and review them online.








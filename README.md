# Gist-This

A [Gist](https://gist.github.com/)+[Slack](https://slack.com/) integration using [WebTask.io](https://webtask.io/) as a communication nexus between the two services.

## How does it work?

You can create your own integrations to quickly create gists in Slack channels. When you send a request from Slack to this webtask, it will create an anonymous file in Gist and send the generated URL to a Slack WebHook. 

For example, you could create a _slash command_ in Slack, like this ```/yourcommand <filename.extension> <my-code-snippet>```, and easily share your gists with your team.

## What do I need?

### Slack Integrations

So, first you'll need to add two integrations in Slack for the Anonymous Gist or three if you want to register Gists with your GitHub account: 

1. a [Slash Command](https://api.slack.com/slash-commands) to send the gist content to our webtask. Take note of the ```token``` generated by this integration, you'll use it when creating the webtask.
2. another Slash Command to send the GitHub token that will be used to associate the Slack user with a GitHub user.
1. an [Incoming WebHook](https://api.slack.com/incoming-webhooks) that will receive the gist result and post a message in our Slack channel. Take note of the WebHook URL as you'll also use it when creating the webtask.

For example, you can create a Slash command ```/gist-this [filename.ext] [code-snippet]``` and an Incoming WebHook that post messages to the #code channel in Slack.

### Webtask.io

So... What is a webtask? An amazingly fast way to 'run code with an HTTP call' as the [home](https://webtask.io) page says. See the [documentation](https://webtask.io/docs/how) to understand how it works.

But, let's just say that it's a way to expose a Node.js module with a URL. You call this URL and your code is executed. Simple as that.

To create your own **webtask container**, sign-up for free [here](https://webtask.io/cli) and follow the steps to install **Webtask CLI** in your computer.

> **Note:** You need to install [Node.js](https://nodejs.org/) first.

#### Creating anonymous gist using gist-this.js

Once **webtask** is installed and initialized, follow these steps:

1. Our module requires 2 _secrets_: the Slack Token from the slash command to validate incoming requests, and the WebHook URL to post the generated gist URL. Create the webtask using the following command:

	````
	wt create https://raw.githubusercontent.com/Verlic/Gist-This/master/webtasks/anonymous-gist-this.js --secret SLACK_TOKEN={command-token} --secret SLACK_URL={webhook-url}
	````
	
1. Once the operation is completed, you will see the webtask URL that you can use to invoke within Slack. Copy this URL and update your Slash command in to send a POST message to the webtask.

#### Creating gists in Slack

You are good to go! Go to a Slack channel, and use your slash command by typing the filename and the content of gist. For example:

````
/gist-this helloworld.js function hello() { console.log ('Hello World'); }
````

This will create an anonymous Gist with the name **helloworld.js** and the code snippet as its body. You can use multiple lines to create larger gists.

> **Note:** You can use this to create documents (e.g. a markdown file, my-doc.md) and review them online.


#### Creating Gists using a GitHub token

So, creating gists directly from Slack is great. But, they are anonymous gists and you can't edit them once they are created. To fix this, we need to store a GitHub token and associate it to the Slack user. This way, the webtask will invoke the API using the token to create gists to your account.

First, you'll need a MongoDb database in the cloud to save the token and the related Slack user. You can get a free one from [MongoLab](https://mongolab.com/) but any service provider will work fine.

Next, the ```gist-register.js`` webtask requires 2 _secrets_: the Slack Token from the slash command to validate incoming requests, and the WebHook URL to post the generated gist URL. Create the webtask using the following command:

	````
	wt create https://raw.githubusercontent.com/Verlic/Gist-This/master/webtasks/gist-register.js --name gist-register --secret SLACK_TOKEN="{register-command-token}" --secret GIST_CONNECTION="{mongodb-connection-string}"
	````
	
Take note of the webtask URL and update your Register Slash command to send a POST message to the webtask.

In Slack, you can type ```/gist-register {token}``` where {token} is your GitHub personal access token. You can generate your token following this [guide](https://help.github.com/articles/creating-an-access-token-for-command-line-use/). This will store your SlackId and your GitHub token.

> **Note**: Take into account that this sample integration does not encrypt your token or your Ids. These are stored as plain text in the MongoDb database.

To invalidate a token, simply use any invalid value as the token. ```/gist-register {invalid-token}```.

Now that the register command and webtask were created, you need to create the gist command webtask. This takes 3 secrets.

	````
	wt create https://raw.githubusercontent.com/Verlic/Gist-This/master/webtasks/gist-this.js --name gist-this --secret SLACK_TOKEN="{register-command-token}" --secret GIST_CONNECTION="{mongodb-connection-string}" --secret SLACK_URL="{webhook-url}"
	````
	
Copy the generated URL and use it in the Slash command in Slack to create gists. Once updated you can use ```/gist-this hello.js function world() { }``` to create your own gists. If you didn't register your token or invalidated your previous one, this command will simply create an anonymous gist.

### Next Steps

I would like to encrypt the tokens when saving data in MongoDb to make the extensions more secure.

### Further reading

- [Another example using Webtasks](https://auth0.com/blog/2015/07/28/if-this-then-node-dot-js-extending-ifttt-with-webtask-dot-io/)
- Check the slides of the Auth0 event: [Rethinking backend with webtasks](https://auth0.com/events/fec15-webtask#!)





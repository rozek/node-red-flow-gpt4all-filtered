# node-red-flow-gpt4all-filtered #

Node-RED Flow (and web page example) for the filtered GPT4All AI model

This repository contains a function node for [Node-RED](https://nodered.org/) which can be used to run the [GPT4All model](https://github.com/nomic-ai/gpt4all) in its "filtered" form using the [official Typescript binding](https://github.com/nomic-ai/gpt4all-ts) within a Node-RED flow. **Inference is done on the CPU** (without requiring any special hardware) and still completes within a few seconds on a reasonably powerful computer.

![GPT4All HTTP Flow](./GPT4All-HTTP-Flow.png)

Having the actual inference as a self-contained function node gives you the possibility to create your own user interface or even use it as part of an autonomous agent.

> Nota bene: these flows do not contain the actual model. You will have to [download your own copy](https://the-eye.eu/public/AI/models/nomic-ai/gpt4all/gpt4all-lora-quantized.bin).

> Just a small note: if you like this work and plan to use it, consider "starring" this repository (you will find the "Star" button on the top right of this page), so that I know which of my repositories to take most care of.

## Installation ##

The actual "heavy lifting" is done by GPT4All's [Typescript binding](https://github.com/nomic-ai/gpt4all-ts).

To install it, simply navigate to the installation folder of your Node-RED server and type

```
npm install gpt4all
```

### Preparing the Model ###

Download the model from the [location given in the docs](https://the-eye.eu/public/AI/models/nomic-ai/gpt4all/gpt4all-lora-quantized.bin) for GPT4All and move it into the folder `~/.nomic`. For Windows users: this 

> Nota bene: right now, the function node supports the 7B model only - but this may easily be changed in the function source

Afterwards, move the file `ggml-alpaca-7b-q4.bin` into the same subfolder `ai` where you already placed the `llama` executable.

### Importing the Function Node ###

Finally, open the Flow Editor of your Node-RED server and import the contents of [Alpaca-Function.json](./Alpaca-Function.json). After deploying your changes, you are ready to run Alpaca inferences directly from within Node-RED.

## Usage ##

The prompt itself and any inference parameters have to be passed as properties of the msg object. The prompt is expected in `msg.payload` and will later be replaced by the result of the inference.

The following properties are supported:

* `payload` - this is the actual prompt 
* `seed` - seed value for the internal pseudo random number generator (integer, default: -1, use random seed for <= 0)
* `threads` - number of threads to use during computation (integer ≧ 1, default: 4)
* `context` - size of the prompt context (integer ≧ 0, default: 512)
* `keep` - number of tokens to keep from the initial prompt (integer ≧ -1, default: 0, -1 = all)
* `predict` - number of tokens to predict (integer ≧ -1, default: 128, -1 = infinity)
* `topk` - top-k sampling limit (integer ≧ 1, default: 40)
* `topp` - top-p sampling limit (0.0...1.0, default: 0.9)
* `temperature` - temperature (0.0...1.0, default: 0.8)
* `batches` - batch size for prompt processing (integer ≧ 1, default: 8)

All properties (except the prompt itself) are optional. If given, they should be strings (even if they contain numbers), this makes it simpler to extract them from an HTTP request.

## Example ##

The file [Alpaca-HTTP-Endpoint.json](./Alpaca-HTTP-Endpoint.json) contains an example which uses the Alpaca function node to answer HTTP requests. The prompt itself and any inference parameters have to be passed as query parameters, the result of the inference will then be returned in the body of the HTTP response.

> Nota bene: the screenshot from above shows a modified version of this flow including an authentication node from the author's [Node-RED Authorization Examples](https://github.com/rozek/node-red-authorization-examples), the flow in [Alpaca-HTTP-Endpoint.json](./Alpaca-HTTP-Endpoint.json) comes without any authentication.

The following parameters are supported (most of them will be copied into a `msg` property of the same name):

* `prompt` - will be copied into `msg.payload`
* `seed` - will be copied into `msg.seed`
* `threads` - will be copied into `msg.threads`
* `context` - will be copied into `msg.context`
* `keep` - will be copied into `msg.keep`
* `predict` - will be copied into `msg.predict`
* `topk` - will be copied into `msg.topk`
* `topp` - will be copied into `msg.topp`
* `temperature` - will be copied into `msg.temperature`
* `batches` - will be copied into `msg.batches`

In order to install this flow, simply open the Flow Editor of your Node-RED server and import the contents of [Alpaca-HTTP-Endpoint.json](./Alpaca-HTTP-Endpoint.json)

### Web Page ###

The file [Alpaca.html](./Alpaca.html) contains a trivial web page which can act as a user interface for the HTTP endpoint.

![Alpaca Screenshot](./Alpaca-Screenshot.png)

Ideally, this page should be served from the same Node-RED server that also accepts the HTTP requests for Alpaca, but this is not strictly necessary.

The input fields `Base URL`, `User Name` and `Password` can be used if web server and Node-RED server are at different locations: just enter the base URL of your Node-RED HTTP endpoint (without the trailing `alpaca`) and, if that server requires basic authentication, your user name and your password in the related input fields before you send your first prompt - otherwise, just leave all these fields empty.

The largest field will show a transcript of your current dialog with the inference node.

Finally, the bottommost input field may be used to enter a prompt - if one is present, the "Send" button becomes enabled: press it to submit your prompt, then wait for a response.

> Nota bene: **inference is still done on the Node-RED server**, not within your browser!

## License ##

[MIT License](LICENSE.md)

# Snippets Sentiment Analysis
This snippet will show you how to identify the sentiment or emotion of the call.  
## About Sentiment Analysis
Easily identify the sentiment and emotion of a call, this is good for POST analysis or with some simple modification, can be used in real-time, to actively route caller.
## Getting Started
You will need a machine with Python installed, the SignalWire SDK, a provisioned SignalWire phone number, and optionally Docker if you decide to run it in a container.

For this demo we will be using Python, but more languages may become available.
- [x] Python
- [x] SignalWire SDK
- [x] SignalWire Phone Number
- [x] Docker (Optional)
----
## Running Sentiment Analysis - How It Works
Sentiment is a score from 0 to 1, that identifies the callers emotion.  You point a signalwire phone number to the /voice_entry endpoint and it will assign a score, and read it back to you.

## Methods and Endpoints
```
Endpoint: /voice_entry
Methods: GET OR POST
Requests to voice_entry, produce a LaML Voice response to gather a clip for analysis.
```

## Setup Your Environment File

1. Copy from 'example.env' and fill in your values
2. Save new file callled '.env'

Your file should look something like this
```
## This is your key for Microsoft cognitive services
MICROSOFT_KEY=
```

## Build and Run on Docker
Lets get started!
1. Use our pre-built image from Docker Hub 
```
For Python:
docker pull signalwire/snippets-sentiment-analysis:python
```
(or build your own image)

1. Build your image
```
docker build -t snippets-sentiment-analysis .
```
2. Run your image
```
docker run --publish 5000:5000 --env-file .env snippets-sentiment-analysis
```
3. The application will run on port 5000

## Build and Run Natively
For Python
```
1. Replace environment variables
2. From command line run, python3 app.py
```

----
# More Documentation
You can find more documentation on LaML, Relay, and all Signalwire APIs at:
- [SignalWire Python SDK](https://github.com/signalwire/signalwire-python)
- [SignalWire API Docs](https://docs.signalwire.com)
- [SignalWire Github](https://gituhb.com/signalwire)
- [Docker - Getting Started](https://docs.docker.com/get-started/)
- [Python - Gettings Started](https://docs.python.org/3/using/index.html)

# Support
If you have any issues or want to engage further about this Signal, please [open an issue on this repo](../../issues) or join our fantastic [Slack community](https://signalwire.community) and chat with others in the SignalWire community!

If you need assistance or support with your SignalWire services please file a support ticket from your Dashboard. 



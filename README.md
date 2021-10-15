# Sentiment Analysis
This guide will show you how to easily identify the sentiment and emotion of a call using the [Python SignalWire SDK](https://developer.signalwire.com/twiml/reference/client-libraries-and-sdks#python). This is good for POST analysis or with some simple modification, it can be used in real-time to route the caller actively. 

In this guide, we will accept calls and send audio to the NLP service allowing for a web application to monitor sentiment in real-time. **Sentiment is a score from 0 to 1 that identifies the caller's emotion.**  You point a SignalWire phone number to the `/voice_entry` endpoint, and it will assign a score and read it back to you. Once a call has three consecutive negative results, the API dispatches an SMS to a supervisor for assistance.

Keep in mind this is an example. However, depending on the sentiment score, you can route the call however your business sees fit, send follow-up surveys for customer satisfaction purposes, begin call recordings, and so much more. The possibilities are truly endless.

# Setup Your Environment File

1. Copy from 'example.env' and fill in your values
2. Save new file called '.env'

Your file should look something like this.
```
# This is your key for Microsoft cognitive services
MICROSOFT_KEY=
```

# Step by Step Code Walkthrough
The first part of our code is defining the function that will actually utilize Microsoft cognitive services API to analyze the sentiment of the user-given message. Calling this function with the `input_text` (recorded speech from the caller) and the `input_language` (the language of the speaker) as parameters will perform the sentiment analysis and store the response in a JSON to make it easier to query later.

```python
def get_sentiment(input_text, input_language):
    base_url = 'https://eastus2.api.cognitive.microsoft.com/text/analytics'
    path = '/v2.0/sentiment'
    constructed_url = base_url + path

    headers = {
        'Ocp-Apim-Subscription-Key': subscription_key,
        'Content-type': 'application/json',
        'X-ClientTraceId': str(uuid.uuid4())
    }

    # You can pass more than one object in body.
    body = {
        'documents': [
            {
                'language': input_language,
                'id': '1',
                'text': input_text
            }
        ]
    }
    response = requests.post(constructed_url, headers=headers, json=body)
    return response.json()
  ```


The route `/voice_entry` will be used to handle incoming calls to your phone number. Here we will use [Gather](https://developer.signalwire.com/twiml/reference/gather) to gather a speech response and [Say](https://developer.signalwire.com/twiml/reference/say) to play a prompt advising the user to begin speaking. After they are doing speaking, we will hang up the call and redirect to the `/sentiment` route specified in the `action` URL. 

```python
@app.route('/voice_entry', methods=['GET','POST'])
def voice_entry():
        
        # instantiate a voice response 
    response = VoiceResponse()
   
    # Prompt user
    gather = Gather(action='/sentiment', input='speech', speechTimeout="auto",  timeout="10", method='GET')

    # Append say to gather to produce TTS
    gather.say("Please say a phrase or statement, and we will than analyze the verbiage and tell you the sentiment. ")

    # Append the gather 
    response.append(gather)

    # Hangup the call
    response.hangup()
        
    # return response
    return str(response)
  ```


In the `/sentiment` route, we will get the `SpeechResult` parameter from the HTTP request to this route. We will use this along with the `input_lang` (English) as parameters for our `get_sentiment()` function. We will round the score to the nearest two digits and convert it into words that represent the emotion of the caller as this is more human-friendly than plain numbers. Lastly, we will use [<Say>](https://developer.signalwire.com/twiml/reference/say) to read the result of the sentiment analysis! 
  
```python
  @app.route('/sentiment', methods=['GET','POST'])
def sentiment():
        # get speech result from user and store in input_text variable
    input_text = request.values.get("SpeechResult")
    input_lang = "en"
    output_text = ""
    output_lang =  "en"
    
        # call get sentiment function with our caller's speech as the input text and english as the language 
    sentimentResponse = get_sentiment(input_text, input_lang)
    
    # Round the score to two digits
    score = round(sentimentResponse['documents'][0]['score'], 2)
     
    response = VoiceResponse()
    sentimentText = ""
    
    # Convert score into easier to understand words
    if score > .9:
        sentimentText = "Extremely Happy"
    elif score > .8:
        sentimentText = "Very Happy"
    elif score > .7:
        sentimentText = "Happy"
    elif score > .6:
        sentimentText = "Slightly Happy"
    elif score > .5:
        sentimentText = "Middle of Road"
    elif score > .4:
        sentimentText = "Slightly Unhappy"
    elif score > .3:
        sentimentText = "Unhappy"
    elif score > .2:
        sentimentText = "Very Unhappy"
    else:
        sentimentText = "Extremely Unhappy"
    
    # Return result to user
    response.say("The sentiment score was " + str(score) + " which indicates user was " +  sentimentText)
    
    # return response
    return str(response)
  ```
  
  
# Build and Run on Docker

1. Use our pre-built image from Docker Hub 
```
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

# Build and Run Natively

To run the application, execute export FLASK_APP=app.py then run flask run.

You may need to use an SSH tunnel for testing this code if running on your local machine. – we recommend [ngrok](https://ngrok.com/). You can learn more about how to use ngrok [here](https://developer.signalwire.com/apis/docs/how-to-test-webhooks-with-ngrok). 

# As Seen on LIVEWire  

If you want to see a live code breakdown, explanation, and demonstration of this guide at work, [click here](https://www.youtube.com/watch?v=88WlmCXFzck&t=4s) or check it out below to watch it on YouTube! While you're there, feel free to take a look at our [YouTube Channel](https://www.youtube.com/channel/UCerXdtujij53AL9IOBFj4SA) to see other LIVEWire code and application breakdowns!
  
# Sign Up Here

If you would like to test this example out, you can create a SignalWire account and space [here](https://m.signalwire.com/signups/new?s=1).

Please feel free to reach out to us on our Community Slack or create a Support ticket if you need guidance!

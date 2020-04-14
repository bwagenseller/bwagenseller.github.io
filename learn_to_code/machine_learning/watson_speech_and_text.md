# Watson (Speech and Text) 

# IBM Cloud Sign-Up

In order to use Watson, you must have an IBM cloud account; to do so:  
1\. Create an account [at ibm.com/cloud](https://www.ibm.com/cloud).  
2\. Click the <font color="purple">Cloud sign-up/log-in</font> button.  
3\. Fill out information.  
4\. Go to your email and click the confirmation link.  
5\. Click <font color="purple">Create resource</font>.  
6\. Click the <font color="green">Speech to Text</font> box.  
7\. Fill out the information and create the resource.  

# Resources 

## General Resources  

- [IBM.com Cloud Page](https://www.ibm.com/cloud)  
- [Getting Started with Watson Speech to Text](https://cloud.ibm.com/services/speech-to-text/crn%3Av1%3Abluemix%3Apublic%3Aspeech-to-text%3Aus-east%3Aa%2Ffd6252ce21a44c96bdfe09bec130a1f5%3A2933fe9c-ba5b-41d8-911c-b908bcd9935a%3A%3A?paneId=gettingStarted&new=true)  *_OR_* [try here](https://cloud.ibm.com/docs/services/speech-to-text?topic=speech-to-text-gettingStarted#getting-started-tutorial).  
- [The Overview for Developers](https://cloud.ibm.com/docs/speech-to-text?topic=speech-to-text-developerOverview).  
- All methods of the <font color="purple">service's API interfaces</font> are in the [API reference](https://cloud.ibm.com/apidocs/speech-to-text/speech-to-text).  
* An <font color="purple">overview for developers</font> is [here](https://cloud.ibm.com/docs/speech-to-text?topic=speech-to-text-developerOverview).  
* An overview of <font color="purple">using SDKs</font> is [here](https://cloud.ibm.com/docs/speech-to-text?topic=watson-using-sdks).  
 * An overview of <font color="purple">using the Java SDK</font> is [here](https://cloud.ibm.com/apidocs/speech-to-text/speech-to-text-data?code=java).  
 * An overview of <font color="purple">using the Python SDK</font> is [here](https://cloud.ibm.com/apidocs/speech-to-text/speech-to-text-data?code=python).  

## Your Credentials

To use Watson, you must use your own credentials. Here is how you can find them: 

1\. Go to the [resource list](https://cloud.ibm.com/resources) page.  
2\. Drop-down the `Services` dropdown.  
3\. Pick the service (for our purposes that will be the `Speech to Text` offering).  
4\. A side-panel will pop-up - click <font color="purple">view full details</font>.  
5\. Here, you will see your unique <font color="purple">API Key</font> and <font color="purple">URL</font>.  

> You can also download an .env file that houses everything you need - it even has additional information not listed normally (`Auth Type` and `iam API Key`). I saved this file to my `~/.credentials` folder.  

# First Attempt: Using cURL

> This was taken from the guide [here](https://cloud.ibm.com/docs/services/speech-to-text?topic=speech-to-text-gettingStarted#getting-started-tutorial).  You _will_ need [credentials](learn_to_code/machine_learning/watson_speech_and_text?id=your-credentials) and a file containing speech (like an mp3).  


The command <font color="orange">cURL</font> (<font color="orange">curl</font>) is a Unix command that allows one to interact with a webpage (to read more on <font color="orange">curl</font>, see [here](https://www.lifewire.com/curl-definition-2184508)).  The [guide](https://cloud.ibm.com/docs/services/speech-to-text?topic=speech-to-text-gettingStarted#getting-started-tutorial) suggests trying to use <font color="orange">curl</font> with no options first:

```
curl -X POST -u "apikey:{apikey}" --header "Content-Type: audio/{file_type}" --data-binary @{path_to_file}{file_name} "{url}/v1/recognize"
```
* Replace `{apikey}` with the API key you got with [your credentials](learn_to_code/machine_learning/watson_speech_and_text?id=your-credentials).
 * Do _not_ use `{` or `}`
* Replace `{path_to_file}` with the path to the file that contains the speech you want to convert to text.
 * Do _not_ use `{` or `}`
* Replace `{file_name}` with the file name that contains the speech you want to convert to text.
 * Do _not_ use `{` or `}`
* Replace `{url}` with the URL you got with [your credentials](learn_to_code/machine_learning/watson_speech_and_text?id=your-credentials).
 * Do _not_ use `{` or `}`
* Replace `{file_type}` with the type of file you want to convert from speech to text.
 * Do _not_ use `{` or `}`
 * This can be, as an example, `flac` or `mp3` (this usually matches the file extension).  

This will print a JSON string:
```
{
  "results": [
    {
      "alternatives": [
        {
          "confidence": 0.96
          "transcript": "several tornadoes touch down as a line of
severe thunderstorms swept through Colorado on Sunday "
        }
      ],
      "final": true
    }
  ],
  "result_index": 0
}
```
* The `transcript` bit is the transcribed words.  
* The `confidence` bit tells you how confident the transcription is (1 = fully confident).  
* Note there is one `alternatives` array for _each_ natural pause in conversation (a period, a question mark, a comma, a semicolon, etc). For example, I tried a line from the [Fallout 4 Intro Cinematic](https://www.youtube.com/watch?v=4NkHQs7ann4) and for the line "In the year 1945, my great-great grandfather, serving in the army," I got this JSON snippet: 
```
...
      {
         "alternatives": [
            {
               "confidence": 0.95, 
               "transcript": "in the in nineteen forty five "
            }
         ], 
         "final": true
      }, 
      {
         "alternatives": [
            {
               "confidence": 0.96, 
               "transcript": "my great great grandfather "
            }
         ], 
         "final": true
      }, 
      {
         "alternatives": [
            {
               "confidence": 0.99, 
               "transcript": "serving in the army "
            }
         ], 
         "final": true
      }, 
...
```
 * As it can be seen, each pause gets its own `alternatives` block.  
 
## Transcribe Audio w/ Options

Since we are effectively sending a request to a URL, we can put options atthe end of the URL - for example, the [guide](https://cloud.ibm.com/docs/services/speech-to-text?topic=speech-to-text-gettingStarted#getting-started-tutorial) suggests trying to use `timestamps=true` and `max_alternatives=3`

> `timestamps=true` seems to put a timestamp (beginning and end) on _each_ word (timestamp relative to the start of the file, in seconds); `max_alternatives` lists a number of alternatives of what could have been said.  

Using the example, we can add in these parameters into the URL:
```
curl -X POST -u "apikey:{apikey}" --header "Content-Type: audio/{file_type}" --data-binary @{path_to_file}{file_name} "{url}/v1/recognize?timestamps=true&max_alternatives=3"
```
* Replace the `{..}` with what we used before.

This returns: 
```
{
  "results": [
    {
      "alternatives": [
        {
          "timestamps": [
            ["several":, 1.0, 1.51],
            ["tornadoes":, 1.51, 2.15],
            ["touch":, 2.15, 2.5],
            . . .
          ]
        },
        {
          "confidence": 0.96
          "transcript": "several tornadoes touch down as a line
of severe thunderstorms swept through Colorado on Sunday "
        },
        {
          "transcript": "several tornadoes touched down as a line
of severe thunderstorms swept through Colorado on Sunday "
        },
        {
          "transcript": "several tornadoes touch down as a line
of severe thunderstorms swept through Colorado and Sunday "
        }
      ],
      "final": true
    }
  ],
  "result_index": 0
}
```
* Strangely, the alternatives do _not_ get their own `alternatives` object - they are lumped into an unnamed section in `alternatives`, which suggests that `alternatives` is a misnomer - it should be something like `phrase`.  
* The confidence is only given for the first alternative - the rest are not reported / returned.  

> [Here](https://cloud.ibm.com/apidocs/speech-to-text/speech-to-text) is more on the API behind speech to text.  
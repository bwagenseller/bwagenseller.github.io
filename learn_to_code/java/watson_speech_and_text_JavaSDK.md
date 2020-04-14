# Watson - Java (Speech and Text) 

This will describe how to use Watson to transcribe speech to text using IBM's Java SDK.  More on the base of this [here](learn_to_code/machine_learning/watson_speech_and_text).  

# Resources

> The general resource listing is located [here](learn_to_code/machine_learning/watson_speech_and_text?id=general-resources).

* An overview of <font color="purple">using the Java SDK</font> is [here](https://cloud.ibm.com/apidocs/speech-to-text/speech-to-text-data?code=java).  


# Importing SDK From Maven

```
<dependency>
  <groupId>com.ibm.watson</groupId>
  <artifactId>ibm-watson</artifactId>
  <version>8.3.0</version>
</dependency>
```


```
import com.ibm.cloud.sdk.core.security.CloudPakForDataAuthenticator;
import com.ibm.watson.speech_to_text.v1.SpeechToText;


public class Main {

    public static void main(String[] args) {

        //CloudPakForDataAuthenticator authenticator = new CloudPakForDataAuthenticator("https://{cpd_cluster_host}{:port}", "{username}", "{password}");
        CloudPakForDataAuthenticator authenticator = new CloudPakForDataAuthenticator("https://{cpd_cluster_host}{:port}", "{username}", "{password}");

        SpeechToText speechToText = new SpeechToText(authenticator);


    }
}

```
---
title: Bing Text to Speech API in Microsoft Cognitive Services | Microsoft Docs
description: Use the Bing Text to Speech API to provide real-time text-to-speech conversion in a variety of voices and languages
services: cognitive-services
author: priyaravi20
manager: yanbo

ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 03/16/2017
ms.author: prrajan
---

# Bing Text to Speech API

## <a name="Introduction"> Introduction</a>
With the Bing Text to Speech API, your application can send HTTP requests to a cloud server, where text is instantly synthesized into human-sounding speech and returned as an audio file. This API can be used in many different contexts to provide real-time text-to-speech conversion in a variety of different voices and languages.  

## <a name="VoiceSynReq"> Voice synthesis request</a>

### <a name="Subscription">Authorization token</a>
Every voice synthesis request requires a JSON Web Token (JWT) access token. The JWT access token is passed through in the speech request header. The token has an expiry time of 10 minutes. For information about subscribing and obtaining API keys that are used to retrieve valid JWT access tokens, see [Get Started for Free](https://www.microsoft.com/cognitive-services/en-US/sign-up?ReturnUrl=/cognitive-services/en-us/subscriptions?productId=%2fproducts%2fBing.Speech.Preview).

The API key is passed to the token service. For example:

```
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

The required header information for token access is as follows.

Name	| Format	| Description
---------|---------|--------
Ocp-Apim-Subscription-Key |	ASCII	| Your subscription key

The token service returns the JWT access token as `text/plain`. Then the JWT is passed as a `Base64 access_token` to the speech endpoint as an authorization header prefixed with the string `Bearer`. For example:

`Authorization: Bearer [Base64 access_token]`

Clients must use the following endpoint to access the text-to-speech service: 

`https://speech.platform.bing.com/synthesize` 

>[!NOTE]
>Until you have acquired an access token with your subscription key as described earlier, this link generates a 403 response error.

### <a name="Http">HTTP headers</a>
The following table shows HTTP headers that are used for voice synthesis requests.

Header   |Value  |Comments 
---------|-------------|-----
Content-Type     |    application/ssml+xml     |      The input content type   
X-Microsoft-OutputFormat     |  **1.** ssml-16khz-16bit-mono-tts <br> **2.** raw-16khz-16bit-mono-pcm <br>**3.** audio-16khz-16kbps-mono-siren <br> **4.** riff-16khz-16kbps-mono-siren <br>  **5.** riff-16khz-16bit-mono-pcm <br> **6.** audio-16khz-128kbitrate-mono-mp3 <br>  **7.** audio-16khz-64kbitrate-mono-mp3 <br> **8.** audio-16khz-32kbitrate-mono-mp3 |       The output audio format  
X-Search-AppId     |    A GUID (hex only, no dashes)     |     An ID that uniquely identifies the client application. This can be the store ID for apps. If one is not available, the ID can be user generated for an application.     
X-Search-ClientID     |     A GUID (hex only, no dashes)    |    An ID that uniquely identifies an application instance per installation.     
User-Agent     |     Application name    |     The application name is required and must be fewer than 255 characters.    

### <a name="InputParam">Input parameters</a>
Inputs to the Bing Text to Speech API are expressed as HTTP query parameters. Parameters in the POST body are treated as Speech Synthesis Markup Language (SSML) content. For a description of the markup used to control aspects of speech such as the language and gender of the speaker, refer to the [SSML W3C Specification](http://www.w3.org/TR/speech-synthesis/). 

The following table provides a complete list of recognized input parameters.  

Parameter   |Description | Values 
---------|---------|----------
VoiceType | Indicates the preferred gender of the voice to speak the synthesized text | Female, Male 
VoiceName | Indicates the processor-specific voice name used to speak the synthesized text | See [Supported locales and voice fonts](#SupLocales).
Locale | Indicates the language used to speak the synthesized text | See [Supported locales and voice fonts](#SupLocales).  
OutputFormat | Indicates the audio format that the text will be synthesized into | SSML16Khz16BitMonoTTS, Raw16khz16bitMonoPCM, Audio16khz16kbpsMonoSiren, Riff16khz16kbpsMonoSiren, Riff16khz16bitMonoPcm, Audio16khz128kbitrateMonoMp3, Audio16khz64kbitrateMonoMp3, Audio16khz32kbitrateMonoMp3 
RequestUri | Indicates the URI of the Internet resource associated with the request | 
AuthorizationToken | Token used to validate the transaction | 
Text | The text to be synthesized |  Unsafe characters should be escaped in accordance with the [W3C URL specifications](http://www.w3.org/Addressing/URL/url-spec.txt).

>[!NOTE]
>Requests with more than one instance of any parameter result in an HTTP 400 error response.

###  <a name="SampleVoiceOR">Example: Voice output request</a>
The following is an example of a voice output request:  
 
```
POST /synthesize
HTTP/1.1
Host: speech.platform.bing.com
Content-Type: audio/wav; samplerate=8000

X-Microsoft-OutputFormat: riff-8khz-8bit-mono-mulaw
Content-Type: text/plain; charset=utf-8
Host: speech.platform.bing.com
Content-Length: 197

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female' name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>Microsoft Bing Voice Output API</voice></speak>
```
 
## <a name="VoiceOutResponse"> Voice output response</a>
The Bing Text to Speech API uses HTTP POST to send audio back to the client. The API response contains the audio stream and the codec, and it matches the requested output format. The audio returned for a given request must not exceed 15 seconds. 
 
### <a name="SuccessfulRecResponse"> Example: Successful synthesis response</a>
The following code is an example of a JSON response to a successful voice search. The comments and formatting of the code are for purposes of this example only and are omitted from the actual response. 
```
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav 

Response audio payload       
```
 
### <a name="RecFailure"> Example: Synthesis failure</a>

The following example code shows a JSON response to a voice-synthesis query failure:

```
HTTP/1.1 400 XML parser error
Content-Type: text/xml
Content-Length: 0
```
 
### <a name="ErrorResponse"> Error responses</a>

Error   | Description 
---------|---------
HTTP/400 Bad Request | A required parameter is missing, empty, or null, or the value passed to either a required or optional parameter is invalid. One reason for getting the “invalid” response is passing a string value that is longer than the allowed length. A brief description of the problematic parameter is included.  
HTTP/401 Unauthorized | The request is not authorized. 
HTTP/413 RequestEntityTooLarge  | The SSML input is larger than what is supported. 
HTTP/502 BadGateway | There is a network-related problem or a server-side issue. 

The following is an example of an error response:
```
HTTP/1.0 400 Bad Request
Content-Length: XXX
Content-Type: text/plain; charset=UTF-8

Voice name not supported
```

## <a name="SampleApp">Sample application</a>
For implementation details, see the [Visual C#.NET Text to Speech sample application](https://github.com/Microsoft/Cognitive-Speech-TTS/blob/master/Samples-Http/CSharp/TTSProgram.cs).  

## <a name="SupLocales">Supported locales and voice fonts</a>

The following table identifies some of the supported locales and related voice fonts.

Locale | Gender | Service name mapping 
---------|--------|------------
ar-EG* | Female | "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)" 
ar-SA | Male | "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)" 
ca-ES | Female | "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)" 
cs-CZ | Male | "Microsoft Server Speech Text to Speech Voice (cs-CZ, Vit)" 
da-DK | Female | "Microsoft Server Speech Text to Speech Voice (da-DK, HelleRUS)" 
de-AT | Male | "Microsoft Server Speech Text to Speech Voice (de-AT, Michael)" 
de-CH | Male | "Microsoft Server Speech Text to Speech Voice (de-CH, Karsten)" 
de-DE | Female | "Microsoft Server Speech Text to Speech Voice (de-DE, Hedda) " 
de-DE | Female | "Microsoft Server Speech Text to Speech Voice (de-DE, HeddaRUS)" 
de-DE | Male | "Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo) " 
el-GR | Male | "Microsoft Server Speech Text to Speech Voice (el-GR, Stefanos)" 
en-AU | Female | "Microsoft Server Speech Text to Speech Voice (en-AU, Catherine) " 
en-AU | Female | "Microsoft Server Speech Text to Speech Voice (en-AU, HayleyRUS)" 
en-CA | Female | "Microsoft Server Speech Text to Speech Voice (en-CA, Linda)" 
en-CA | Female | "Microsoft Server Speech Text to Speech Voice (en-CA, HeatherRUS)" 
en-GB | Female | "Microsoft Server Speech Text to Speech Voice (en-GB, Susan, Apollo)" 
en-GB | Female | "Microsoft Server Speech Text to Speech Voice (en-GB, HazelRUS)" 
en-GB | Male | "Microsoft Server Speech Text to Speech Voice (en-GB, George, Apollo)" 
en-IE | Male | "Microsoft Server Speech Text to Speech Voice (en-IE, Shaun)" 
en-IN | Female | "Microsoft Server Speech Text to Speech Voice (en-IN, Heera, Apollo)" 
en-IN | Female | "Microsoft Server Speech Text to Speech Voice (en-IN, PriyaRUS)" 
en-IN | Male | "Microsoft Server Speech Text to Speech Voice (en-IN, Ravi, Apollo)  " 
en-US | Female | "Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)" 
en-US | Female | "Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)"
en-US | Male | "Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)" 
es-ES | Female | "Microsoft Server Speech Text to Speech Voice (es-ES, Laura, Apollo)" 
es-ES | Female | "Microsoft Server Speech Text to Speech Voice (es-ES, HelenaRUS)" 
es-ES | Male | "Microsoft Server Speech Text to Speech Voice (es-ES, Pablo, Apollo)" 
es-MX | Female | "Microsoft Server Speech Text to Speech Voice (es-MX, HildaRUS)" 
es-MX | Male | "Microsoft Server Speech Text to Speech Voice (es-MX, Raul, Apollo)" 
fi-FI | Female | "Microsoft Server Speech Text to Speech Voice (fi-FI, HeidiRUS)" 
fr-CA | Female | "Microsoft Server Speech Text to Speech Voice (fr-CA, Caroline)" 
fr-CA | Female | "Microsoft Server Speech Text to Speech Voice (fr-CA, HarmonieRUS)" 
fr-CH | Male | "Microsoft Server Speech Text to Speech Voice (fr-CH, Guillaume)" 
fr-FR | Female | "Microsoft Server Speech Text to Speech Voice (fr-FR, Julie, Apollo)" 
fr-FR | Female | "Microsoft Server Speech Text to Speech Voice (fr-FR, HortenseRUS)" 
fr-FR | Male | "Microsoft Server Speech Text to Speech Voice (fr-FR, Paul, Apollo)" 
he-IL| Male| "Microsoft Server Speech Text to Speech Voice (he-IL, Asaf)"
hi-IN | Female | "Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana, Apollo)" 
hi-IN | Female | "Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana)" 
hi-IN | Male | "Microsoft Server Speech Text to Speech Voice (hi-IN, Hemant)" 
hu-HU | Male | "Microsoft Server Speech Text to Speech Voice (hu-HU, Szabolcs)" 
id-ID | Male | "Microsoft Server Speech Text to Speech Voice (id-ID, Andika)" 
it-IT | Male | "Microsoft Server Speech Text to Speech Voice (it-IT, Cosimo, Apollo)" 
ja-JP | Female | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ayumi, Apollo)" 
ja-JP | Male | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ichiro, Apollo)" 
ko-KR | Female | "Microsoft Server Speech Text to Speech Voice (ko-KR, HeamiRUS)" 
nb-NO | Female | "Microsoft Server Speech Text to Speech Voice (nb-NO, HuldaRUS)" 
nl-NL | Female | "Microsoft Server Speech Text to Speech Voice (nl-NL, HannaRUS)" 
pl-PL | Female | "Microsoft Server Speech Text to Speech Voice (pl-PL, PaulinaRUS)" 
pt-BR | Female | "Microsoft Server Speech Text to Speech Voice (pt-BR, HeloisaRUS)" 
pt-BR | Male | "Microsoft Server Speech Text to Speech Voice (pt-BR, Daniel, Apollo)" 
pt-PT | Female | "Microsoft Server Speech Text to Speech Voice (pt-PT, HeliaRUS)" 
ro-RO | Male | "Microsoft Server Speech Text to Speech Voice (ro-RO, Andrei)" 
ru-RU | Female | "Microsoft Server Speech Text to Speech Voice (ru-RU, Irina, Apollo)" 
ru-RU | Male | "Microsoft Server Speech Text to Speech Voice (ru-RU, Pavel, Apollo)" 
sk-SK | Male | "Microsoft Server Speech Text to Speech Voice (sk-SK, Filip)" 
sv-SE | Female | "Microsoft Server Speech Text to Speech Voice (sv-SE, HedvigRUS)" 
th-TH | Male | "Microsoft Server Speech Text to Speech Voice (th-TH, Pattara)" 
tr-TR | Female | "Microsoft Server Speech Text to Speech Voice (tr-TR, SedaRUS)" 
zh-CN | Female | "Microsoft Server Speech Text to Speech Voice (zh-CN, HuihuiRUS)" 
zh-CN | Female | "Microsoft Server Speech Text to Speech Voice (zh-CN, Yaoyao, Apollo)" 
zh-CN | Male | "Microsoft Server Speech Text to Speech Voice (zh-CN, Kangkang, Apollo)" 
zh-HK | Female | "Microsoft Server Speech Text to Speech Voice (zh-HK, Tracy, Apollo)" 
zh-HK | Female | "Microsoft Server Speech Text to Speech Voice (zh-HK, TracyRUS)" 
zh-HK | Male | "Microsoft Server Speech Text to Speech Voice (zh-HK, Danny, Apollo)" 
zh-TW | Female | "Microsoft Server Speech Text to Speech Voice (zh-TW, Yating, Apollo)" 
zh-TW | Female | "Microsoft Server Speech Text to Speech Voice (zh-TW, HanHanRUS)" 
zh-TW | Male | "Microsoft Server Speech Text to Speech Voice (zh-TW, Zhiwei, Apollo)" 
 *ar-EG supports Modern Standard Arabic (MSA).

## <a name="TrouNSupport"> Troubleshooting and support</a>

Post all questions and issues to the [Bing Speech Service](https://social.msdn.microsoft.com/Forums/en-US/home?forum=SpeechService) MSDN forum. Include complete details, such as: 
* An example of the full request string.
* If applicable, the full output of a failed request, which includes log IDs.
* The percentage of requests that are failing.


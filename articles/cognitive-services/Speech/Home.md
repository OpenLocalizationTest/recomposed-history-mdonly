---
title: Bing Speech API in Microsoft Cognitive Services | Microsoft Docs
description: Use the Bing Speech API to add speech-driven actions to your apps, including real-time interaction with users.
services: cognitive-services
author: priyaravi20
manager: yanbo

ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 02/28/2017
ms.author: prrajan
---
# Bing Speech API Overview
Microsoft’s *Speech to Text* and *Text to Speech* cloud offerings help you put speech to work in your application. Microsoft's
Speech APIs can transcribe speech *to* text and can generate speech *from* text, enabling you to create powerful experiences that will delight your users.

* **Speech to Text** APIs convert human speech to text that can be used as input or commands to control your application.
* **Text to Speech** APIs convert text to audio streams that can be played back to the user of your application.

## Speech to Text (Speech Recognition)
The *Speech to Text* APIs *transcribe* audio streams into text that your application can display to the user or act upon as command input. The *Speech To Text* APIs come in two flavors.

* A REST API, useful for apps that need to convert short spoken commands to text but do not need real-time streaming or simultaneous user feedback. The REST API uses 
[HTTP chunked-transfer encoding](https://en.wikipedia.org/wiki/Chunked_transfer_encoding) to send the audio bytes to the service.
* A [WebSocket](https://en.wikipedia.org/wiki/WebSocket) API, useful for apps that need to convert longer audio to text or that want an
improved user experience by leveraging the power of the full-duplex WebSocket connection through features like speech recognition hypotheses.

Both *Speech to Text* APIs can enrich the transcribed text results by adding capitalization and punctuation, masking profanity, and text normalization.

### Comparing API Options for Speech Recognition

| Feature | WebSocket API | REST API |
|-----|-----|-----|
| Speech hypotheses | Yes | No |
| Continuous recognition | Yes | No |
| Maximum audio input | 10 minutes of audio | 15 seconds of audio |
| Service detects when speech ends | Yes| No |
| Subscription key authorization | Yes | No |

### Speech Recognition Modes
Microsoft's *Speech to Text* APIs support multiple modes of speech recognition. Choose the mode that produces the best recognition results for your application.

| Mode | Description |
|---|---|
| *interactive* | "Command and control" recognition for interactive user application scenarios. Users speak short phrases intended as commands to an application. |
| *dictation* | Continuous recognition for dictation scenarios. Users speak longer sentences that are displayed as text. Users adopt a more formal speaking style. |
| *conversation* | Continuous recognition for transcribing conversations between humans. Users adopt a less formal speaking style and may alternate between longer sentences and shorter phrases. |

Refer to [Recognition Modes](api-reference-rest/bingvoicerecognition.md#recognition-modes) in the API reference for more information.

### Speech Recognition Supported languages  
The *Speech to Text* APIs support many spoken languages in multiple dialects. The languages and locales supported by the *Speech to Text* APIs include:    

| language-Country | language-Country | language-Country | language-Country    
|---------|----------|--------|--------------- 
| ar-EG    |  en-NZ  | it-IT  |  ru-RU  
| ca-ES    |  en-US  | ja-JP  |  sv-SE  
| da-DK    |  es-ES  | ko-KR  |  zh-CN  
| de-DE    |  es-MX  | nb-NO  |  zh-HK  
| en-AU    |  fi-FI  | nl-NL  |  zh-TW  
| en-CA    |  fr-CA  | pl-PL  |      
| en-GB    |  fr-FR  | pt-BR  |        
| en-IN    |  hi-IN  | pt-PT  |  

Refer to [Recognition Languages](api-reference-rest/bingvoicerecognition.md#recognition-languages) 
for the full list of supported languages for each recognition mode. 

## Text to Speech (Speech Synthesis)
*Text to Speech* APIs use REST to convert structured text to an audio stream. The APIs provide real-time text to speech conversion in a variety of different 
voices and languages.

### Supported languages  
The languages and locales supported by *Text to Speech* API include:  

| language-Country |language-Country | language-Country | language-Country  
| ---------|----------|------------|------------  
| ar-EG    |   en-GB  |   hi-IN  |   pt-PT  
| ar-SA    |   en-IE  |   hu-HU  |   ro-RO  
| ca-ES    |   en-IN  |   id-ID  |   ru-RU  
| cs-CZ    |   en-US  |   it-IT  |   sk-SK  
| da-DK    |   es-ES  |   ja-JP  |   sv-SE  
| de-AT    |   es-MX  |   ko-KR  |   th-TH  
| de-CH    |   fi-FI  |   nb-NO  |   tr-TR  
| de-DE    |   fr-CA  |   nl-NL  |   zh-CN  
| el-GR    |   fr-CH  |   pl-PL  |   zh-HK  
| en-AU    |   fr-FR  |   pt-BR  |   zh-TW    

Refer to [Supported Locales and Voice Fonts](api-reference-rest/bingvoiceoutput.md#a-namesuplocalesasupported-locales-and-voice-fonts) for 
the full list of supported languages and voices.

### Text to Speech API 
The maximum amount of audio returned for any single request is 15 seconds. 

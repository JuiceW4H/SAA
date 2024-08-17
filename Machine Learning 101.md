# Machine Learning 101

## Amazon Comprehend

* It develops insights by recognizing the entities, key phrases, language, sentiments and other common elements of the document provided.
* Makes use of Natural Language Processing (NLP).
* Based on either pre-trained or custom models.
* Capable of doing real-time analysis for small workloads and Async jobs for larger workloads.
* Accessed via Console, CLI and APIs.

## Amazon Kendra

* Kendra is an intelligent search service.
* Designed to mimic interaction with a human expert.
* Supports a wide range of question types (Factoid, Descriptive, Keyword)
* uses Natural Language Understanding (NLU) to understand context or intent.

Factoid
- Who, What, Where

**
**
Index
- searchable data organized in an efficient way.

Data Source
- location of where your data lives.
- Kendra connects and indexes from this location.

* Synchronize with index based on a schedule (Updating essentially).

**## Amazon Lex**

* Amazon Lex allows you to create interactive chatbots.
* Provides Text or Voice conversational interfaces.
* Lex powers the Alexa service.
* Provides Automatic Speech Recognition (ASR) and Natural Language Understanding (NLU).

Automatic Speech Recognition (ASR)
- speech to text

Natural Language Understanding (NLU)
- allows Lex to discover your intent.

Bots
- designed to interactively converse in 1 or more languages.

Intent
- an action the user wants to perform.

Slot
- parameters essentially.

## Amazon Polly

* Amazon Polly converts text into life-like speech (text-to-speech).
* Does not perform translation.
* Provides Standard TTS and Neural TTS.
* Output formats support MP3, Ogg Vorbis, PCM and etc.
* Capable of using the Speech Synthesis Markup Language (SSML).

Standard TTS
- Concatenative (Phonemes)

Neural TTS
- uses phonemes to spectrograms to vocoder which generates the audio.
- more humans sounding speech audio.

Speech Synthesis Markup Language (SSML)
- a way to provide additional context within the text so you can control how Polly generates speech.
- allows you to modify pronunciation and speech dynamics.

## Amazon Rekognition

* Rekognition is a deep learning based image and video analysis product (Object Detection/Classification).
* Pay as you go pricing per image or per minute of video.
* Integrates with applications and event-driven.
* Can analyze live video streams through Kinesis Video Streams.

**Rekognition Architecture**
![Machine Learning 101-08-09-2024](images/Machine%20Learning%20101-08-09-2024.png)

## Amazon Textract

* Amazon Textract is used to detect and analyze text contained in input documents.
* Input Documents supported are JPEG, PNG, PDF, or TIFF.
* Output is Extracted Text, Structure of that text, and Analysis.
* Pay per usage and custom pricing for large volume usage.

Use Case

* Detection of Texts.
* Relationship between texts.
* Generates metadata.
* Document analysis (Names, Address, Birthdates…)
* Receipt Analysis (Prices, Vendor, Line Items, Dates…)
* Identity Documents (Abstracts Fields… i.e. DocumentID)

## Amazon Transcribe

* An automatic Speech Recognition (ASR) service or a essentially a speech-to-text service.
* Input is audio and output is text.
* You can configure custom vocabularies and language models.
* pay as you use or per second of transcribed audio.

## Amazon Translate

* Amazon Translate is a text translation service based on machine learning.
* Translates text from one language to another.
* Can auto-detect source text language.

Encoder
- reads the source text and then outputs a semantic representation or meaning of the source text.

Decoder
- reads the meaning and writes at the target language

## Amazon Forecast 101

* Amazon Forecast provides forecasting for time-series data.
* Essentially used for prediction based on historical data presented to it (Linear Regression).
* Imports Historical Data and Related Data.
* Outputs a forecast and forecast explainability.
* Accessible via Web Console, CLI, APIs, and Python SDK.

Forecast
- allows you to trend out future demand for a particular data.

Forecast Explainability
- allows you to extract reasoning for changes in demand.

## Amazon Fraud Detector

* A fully managed fraud detection service.
* Allows you to look at various historical trends and other related data and identify any potential fraud as it relates to certain online activities.
* Simply look for outliers.
* Upload historical data and choose a model type.
* Things are scored and rules are used to react to a score based on business activity.

**Model Types**

1. Online Fraud
	- designed for when you have little historical data and looking to identify any problematic events.

1. Transaction Fraud
	- ideal for when you have a transactional historical for certain customers.
	- identifying suspect payments through their transactions.

1. Account Takeover
	- used to identify any phishing or other social based attacks.

## Amazon SageMaker

* SageMaker is an implementation of a fully managed Machine Learning (ML) service.
* It helps you with the process of developing and using ML models.
* Fetch, Clean, Prepare, Train, Evaluate, Deploy, Monitor/Collect.
* Capable of Hosting ML models as Endpoints.
* Resources that SageMaker creates have a cost.

SageMaker Studio
- an IDE for the entire ML lifecycle.
- allows you to build, train, debug, and monitor models.

SageMaker Domain
- groupings for a particular project.

Containers
- Docker containers deployed to ML configured EC2 instances.


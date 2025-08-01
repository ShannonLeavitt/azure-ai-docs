---
title: Test recognition quality of a custom speech model - Speech service
titleSuffix: Azure AI services
description: Custom speech lets you qualitatively inspect the recognition quality of a model. You can play back uploaded audio and determine if the provided recognition result is correct.
author: eric-urban
manager: nitinme
ms.service: azure-ai-speech
ms.topic: how-to
ms.date: 5/19/2025
ms.author: eur
zone_pivot_groups: foundry-speech-studio-cli-rest
#Customer intent: As a developer, I want to test the recognition quality of a custom speech model so that I can determine if the provided recognition result is correct.
---

# Test recognition quality of a custom speech model

You can inspect the recognition quality of a custom speech model. You can play back uploaded audio and determine if the provided recognition result is correct. After a test is successfully created, you can see how a model transcribed the audio dataset, or compare results from two models side by side.

Side-by-side model testing is useful to validate which speech recognition model is best for an application. For an objective measure of accuracy, which requires transcription datasets input, see [Test model quantitatively](how-to-custom-speech-evaluate-data.md).

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## Create a test

After you [upload training and testing datasets](how-to-custom-speech-upload-data.md), you can create a test.

::: zone pivot="ai-foundry-portal"

To test your fine-tuned custom speech model, follow these steps:

1. Sign in to the [Azure AI Foundry portal](https://ai.azure.com/?cid=learnDocs).
1. Select **Fine-tuning** from the left pane and then select **AI Service fine-tuning**.
1. Select the custom speech fine-tuning task (by model name) that you [started as described in the how to start custom speech fine-tuning article](./how-to-custom-speech-create-project.md).
1. Select **Test models** > **+ Create test**. 

    :::image type="content" source="./media/custom-speech/ai-foundry/new-fine-tune-test-model.png" alt-text="Screenshot of the page with an option to test your custom speech model." lightbox="./media/custom-speech/ai-foundry/new-fine-tune-test-model.png":::

1. In the **Create a new test** wizard, select the test type. For a quality test, select **Inspect quality (Audio-only data)**. Then select **Next**.
1. Select the data that you want to use for testing. Then select **Next**.
1. Select up to two models to evaluate and compare accuracy. In this example, we select the model that we trained and the base model. Then select **Next**.

    :::image type="content" source="./media/custom-speech/ai-foundry/new-fine-tune-test-model-select-models.png" alt-text="Screenshot of the page with an option to select up to two models to evaluate and compare accuracy." lightbox="./media/custom-speech/ai-foundry/new-fine-tune-test-model-select-models.png":::

1. Enter a name and description for the test. Then select **Next**.
1. Review the settings and select **Create test**. You're taken back to the **Test models** page. The status of the data is **Processing**.

::: zone-end

::: zone pivot="speech-studio"

Follow these instructions to create a test:

1. Sign in to the [Speech Studio](https://aka.ms/speechstudio/customspeech).
1. Navigate to **Speech Studio** > **Custom speech** and select your project name from the list.
1. Select **Test models** > **Create new test**.
1. Select **Inspect quality (Audio-only data)** > **Next**. 
1. Choose an audio dataset that you'd like to use for testing, and then select **Next**. If there aren't any datasets available, cancel the setup, and then go to the **Speech datasets** menu to [upload datasets](how-to-custom-speech-upload-data.md).

    :::image type="content" source="media/custom-speech/custom-speech-choose-test-data.png" alt-text="Screenshot of choosing a dataset dialog":::

1. Choose one or two models to evaluate and compare accuracy.
1. Enter the test name and description, and then select **Next**.
1. Review your settings, and then select **Save and close**.

::: zone-end

::: zone pivot="speech-cli"

To create a test, use the `spx csr evaluation create` command. Construct the request parameters according to the following instructions:

- Set the `project` property to the ID of an existing project. This property is recommended so that you can also view the test in the [Azure AI Foundry portal](https://ai.azure.com/?cid=learnDocs). You can run the `spx csr project list` command to get available projects.
- Set the required `model1` property to the ID of a model that you want to test.
- Set the required `model2` property to the ID of another model that you want to test. If you don't want to compare two models, use the same model for both `model1` and `model2`.
- Set the required `dataset` property to the ID of a dataset that you want to use for the test.
- Set the `language` property, otherwise the Speech CLI sets "en-US" by default. This parameter should be the locale of the dataset contents. The locale can't be changed later. The Speech CLI `language` property corresponds to the `locale` property in the JSON request and response.
- Set the required `name` property. This parameter is the name that is displayed in the [Azure AI Foundry portal](https://ai.azure.com/?cid=learnDocs). The Speech CLI `name` property corresponds to the `displayName` property in the JSON request and response.

Here's an example Speech CLI command that creates a test:

```azurecli-interactive
spx csr evaluation create --api-version v3.2 --project aaaabbbb-0000-cccc-1111-dddd2222eeee --dataset bbbbcccc-1111-dddd-2222-eeee3333ffff --model1 ccccdddd-2222-eeee-3333-ffff4444aaaa --model2 ccccdddd-2222-eeee-3333-ffff4444aaaa --name "My Inspection" --description "My Inspection Description"
```

You should receive a response body in the following format:

```json
{
  "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/evaluations/ddddeeee-3333-ffff-4444-aaaa5555bbbb",
  "model1": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/models/base/ccccdddd-2222-eeee-3333-ffff4444aaaa"
  },
  "model2": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/models/base/ccccdddd-2222-eeee-3333-ffff4444aaaa"
  },
  "dataset": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/datasets/bbbbcccc-1111-dddd-2222-eeee3333ffff"
  },
  "transcription2": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/transcriptions/eeeeffff-4444-aaaa-5555-bbbb6666cccc"
  },
  "transcription1": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/transcriptions/eeeeffff-4444-aaaa-5555-bbbb6666cccc"
  },
  "project": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/projects/aaaabbbb-0000-cccc-1111-dddd2222eeee"
  },
  "links": {
    "files": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/evaluations/9c06d5b1-213f-4a16-9069-bc86efacdaac/files"
  },
  "properties": {
    "wordErrorRate1": -1.0,
    "sentenceErrorRate1": -1.0,
    "sentenceCount1": -1,
    "wordCount1": -1,
    "correctWordCount1": -1,
    "wordSubstitutionCount1": -1,
    "wordDeletionCount1": -1,
    "wordInsertionCount1": -1,
    "wordErrorRate2": -1.0,
    "sentenceErrorRate2": -1.0,
    "sentenceCount2": -1,
    "wordCount2": -1,
    "correctWordCount2": -1,
    "wordSubstitutionCount2": -1,
    "wordDeletionCount2": -1,
    "wordInsertionCount2": -1
  },
  "lastActionDateTime": "2024-07-14T21:21:39Z",
  "status": "NotStarted",
  "createdDateTime": "2024-07-14T21:21:39Z",
  "locale": "en-US",
  "displayName": "My Inspection",
  "description": "My Inspection Description"
}
```

The top-level `self` property in the response body is the evaluation's URI. Use this URI to get details about the project and test results. You also use this URI to update or delete the evaluation.

For Speech CLI help with evaluations, run the following command:

```azurecli-interactive
spx help csr evaluation
```

::: zone-end

::: zone pivot="rest-api"

To create a test, use the [Evaluations_Create](/rest/api/speechtotext/evaluations/create) operation of the [Speech to text REST API](rest-speech-to-text.md). Construct the request body according to the following instructions:

- Set the `project` property to the URI of an existing project. This property is recommended so that you can also view the test in the [Azure AI Foundry portal](https://ai.azure.com/?cid=learnDocs). You can make a [Projects_List](/rest/api/speechtotext/projects/list) request to get available projects.
- Set the required `model1` property to the URI of a model that you want to test. 
- Set the required `model2` property to the URI of another model that you want to test. If you don't want to compare two models, use the same model for both `model1` and `model2`.
- Set the required `dataset` property to the URI of a dataset that you want to use for the test.
- Set the required `locale` property. This property should be the locale of the dataset contents. The locale can't be changed later.
- Set the required `displayName` property. This property is the name that is displayed in the [Azure AI Foundry portal](https://ai.azure.com/?cid=learnDocs).

Make an HTTP POST request using the URI as shown in the following example. Replace `YourSpeechResoureKey` with your Speech resource key, replace `YourServiceRegion` with your Speech resource region, and set the request body properties as previously described.

```azurecli-interactive
curl -v -X POST -H "Ocp-Apim-Subscription-Key: YourSpeechResoureKey" -H "Content-Type: application/json" -d '{
  "model1": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/models/ccccdddd-2222-eeee-3333-ffff4444aaaa"
  },
  "model2": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/models/base/ccccdddd-2222-eeee-3333-ffff4444aaaa"
  },
  "dataset": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/datasets/bbbbcccc-1111-dddd-2222-eeee3333ffff"
  },
  "project": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/projects/aaaabbbb-0000-cccc-1111-dddd2222eeee"
  },
  "displayName": "My Inspection",
  "description": "My Inspection Description",
  "locale": "en-US"
}'  "https://YourServiceRegion.api.cognitive.microsoft.com/speechtotext/v3.2/evaluations"
```

You should receive a response body in the following format:

```json
{
  "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/evaluations/ddddeeee-3333-ffff-4444-aaaa5555bbbb",
  "model1": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/models/base/ccccdddd-2222-eeee-3333-ffff4444aaaa"
  },
  "model2": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/models/base/ccccdddd-2222-eeee-3333-ffff4444aaaa"
  },
  "dataset": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/datasets/bbbbcccc-1111-dddd-2222-eeee3333ffff"
  },
  "transcription2": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/transcriptions/eeeeffff-4444-aaaa-5555-bbbb6666cccc"
  },
  "transcription1": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/transcriptions/eeeeffff-4444-aaaa-5555-bbbb6666cccc"
  },
  "project": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/projects/aaaabbbb-0000-cccc-1111-dddd2222eeee"
  },
  "links": {
    "files": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/evaluations/9c06d5b1-213f-4a16-9069-bc86efacdaac/files"
  },
  "properties": {
    "wordErrorRate1": -1.0,
    "sentenceErrorRate1": -1.0,
    "sentenceCount1": -1,
    "wordCount1": -1,
    "correctWordCount1": -1,
    "wordSubstitutionCount1": -1,
    "wordDeletionCount1": -1,
    "wordInsertionCount1": -1,
    "wordErrorRate2": -1.0,
    "sentenceErrorRate2": -1.0,
    "sentenceCount2": -1,
    "wordCount2": -1,
    "correctWordCount2": -1,
    "wordSubstitutionCount2": -1,
    "wordDeletionCount2": -1,
    "wordInsertionCount2": -1
  },
  "lastActionDateTime": "2024-07-14T21:21:39Z",
  "status": "NotStarted",
  "createdDateTime": "2024-07-14T21:21:39Z",
  "locale": "en-US",
  "displayName": "My Inspection",
  "description": "My Inspection Description"
}
```

The top-level `self` property in the response body is the evaluation's URI. Use this URI to [get](/rest/api/speechtotext/evaluations/get) details about the evaluation's project and test results. You also use this URI to [update](/rest/api/speechtotext/evaluations/update) or [delete](/rest/api/speechtotext/evaluations/delete) the evaluation.

::: zone-end


## Get test results

You should get the test results and [inspect](#compare-transcription-with-audio) the audio datasets compared to transcription results for each model.

::: zone pivot="ai-foundry-portal"

When the test status is **Succeeded**, you can view the results. Select the test to view the results.

::: zone-end

::: zone pivot="speech-studio"

Follow these steps to get test results:

1. Sign in to the [Speech Studio](https://aka.ms/speechstudio/customspeech).
1. Select **Custom speech** > Your project name > **Test models**.
1. Select the link by test name.
1. After the test is complete, as indicated by the status set to *Succeeded*, you should see results that include the WER number for each tested model.

This page lists all the utterances in your dataset and the recognition results, alongside the transcription from the submitted dataset. You can toggle various error types, including insertion, deletion, and substitution. By listening to the audio and comparing recognition results in each column, you can decide which model meets your needs and determine where more training and improvements are required.

::: zone-end

::: zone pivot="speech-cli"

To get test results, use the `spx csr evaluation status` command. Construct the request parameters according to the following instructions:

- Set the required `evaluation` property to the ID of the evaluation that you want to get test results.

Here's an example Speech CLI command that gets test results:

```azurecli-interactive
spx csr evaluation status --api-version v3.2 --evaluation ddddeeee-3333-ffff-4444-aaaa5555bbbb
```

The models, audio dataset, transcriptions, and more details are returned in the response body.

You should receive a response body in the following format:

```json
{
  "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/evaluations/ddddeeee-3333-ffff-4444-aaaa5555bbbb",
  "model1": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/models/base/ccccdddd-2222-eeee-3333-ffff4444aaaa"
  },
  "model2": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/models/base/ccccdddd-2222-eeee-3333-ffff4444aaaa"
  },
  "dataset": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/datasets/bbbbcccc-1111-dddd-2222-eeee3333ffff"
  },
  "transcription2": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/transcriptions/eeeeffff-4444-aaaa-5555-bbbb6666cccc"
  },
  "transcription1": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/transcriptions/eeeeffff-4444-aaaa-5555-bbbb6666cccc"
  },
  "project": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/projects/aaaabbbb-0000-cccc-1111-dddd2222eeee"
  },
  "links": {
    "files": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/evaluations/9c06d5b1-213f-4a16-9069-bc86efacdaac/files"
  },
  "properties": {
    "wordErrorRate1": 0.028900000000000002,
    "sentenceErrorRate1": 0.667,
    "tokenErrorRate1": 0.12119999999999999,
    "sentenceCount1": 3,
    "wordCount1": 173,
    "correctWordCount1": 170,
    "wordSubstitutionCount1": 2,
    "wordDeletionCount1": 1,
    "wordInsertionCount1": 2,
    "tokenCount1": 165,
    "correctTokenCount1": 145,
    "tokenSubstitutionCount1": 10,
    "tokenDeletionCount1": 1,
    "tokenInsertionCount1": 9,
    "tokenErrors1": {
      "punctuation": {
        "numberOfEdits": 4,
        "percentageOfAllEdits": 20.0
      },
      "capitalization": {
        "numberOfEdits": 2,
        "percentageOfAllEdits": 10.0
      },
      "inverseTextNormalization": {
        "numberOfEdits": 1,
        "percentageOfAllEdits": 5.0
      },
      "lexical": {
        "numberOfEdits": 12,
        "percentageOfAllEdits": 12.0
      },
      "others": {
        "numberOfEdits": 1,
        "percentageOfAllEdits": 5.0
      }
    },
    "wordErrorRate2": 0.028900000000000002,
    "sentenceErrorRate2": 0.667,
    "tokenErrorRate2": 0.12119999999999999,
    "sentenceCount2": 3,
    "wordCount2": 173,
    "correctWordCount2": 170,
    "wordSubstitutionCount2": 2,
    "wordDeletionCount2": 1,
    "wordInsertionCount2": 2,
    "tokenCount2": 165,
    "correctTokenCount2": 145,
    "tokenSubstitutionCount2": 10,
    "tokenDeletionCount2": 1,
    "tokenInsertionCount2": 9,
    "tokenErrors2": {
      "punctuation": {
        "numberOfEdits": 4,
        "percentageOfAllEdits": 20.0
      },
      "capitalization": {
        "numberOfEdits": 2,
        "percentageOfAllEdits": 10.0
      },
      "inverseTextNormalization": {
        "numberOfEdits": 1,
        "percentageOfAllEdits": 5.0
      },
      "lexical": {
        "numberOfEdits": 12,
        "percentageOfAllEdits": 12.0
      },
      "others": {
        "numberOfEdits": 1,
        "percentageOfAllEdits": 5.0
      }
    }
  },
  "lastActionDateTime": "2024-07-14T21:22:45Z",
  "status": "Succeeded",
  "createdDateTime": "2024-07-14T21:21:39Z",
  "locale": "en-US",
  "displayName": "My Inspection",
  "description": "My Inspection Description"
}
```

For Speech CLI help with evaluations, run the following command:

```azurecli-interactive
spx help csr evaluation
```

::: zone-end

::: zone pivot="rest-api"

To get test results, start by using the [Evaluations_Get](/rest/api/speechtotext/evaluations/get) operation of the [Speech to text REST API](rest-speech-to-text.md).

Make an HTTP GET request using the URI as shown in the following example. Replace `YourEvaluationId` with your evaluation ID, replace `YourSpeechResoureKey` with your Speech resource key, and replace `YourServiceRegion` with your Speech resource region.

```azurecli-interactive
curl -v -X GET "https://YourServiceRegion.api.cognitive.microsoft.com/speechtotext/v3.2/evaluations/YourEvaluationId" -H "Ocp-Apim-Subscription-Key: YourSpeechResoureKey"
```

The models, audio dataset, transcriptions, and more details are returned in the response body.

You should receive a response body in the following format:

```json
{
  "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/evaluations/ddddeeee-3333-ffff-4444-aaaa5555bbbb",
  "model1": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/models/base/ccccdddd-2222-eeee-3333-ffff4444aaaa"
  },
  "model2": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/models/base/ccccdddd-2222-eeee-3333-ffff4444aaaa"
  },
  "dataset": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/datasets/bbbbcccc-1111-dddd-2222-eeee3333ffff"
  },
  "transcription2": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/transcriptions/eeeeffff-4444-aaaa-5555-bbbb6666cccc"
  },
  "transcription1": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/transcriptions/eeeeffff-4444-aaaa-5555-bbbb6666cccc"
  },
  "project": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/projects/aaaabbbb-0000-cccc-1111-dddd2222eeee"
  },
  "links": {
    "files": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.2/evaluations/9c06d5b1-213f-4a16-9069-bc86efacdaac/files"
  },
  "properties": {
    "wordErrorRate1": 0.028900000000000002,
    "sentenceErrorRate1": 0.667,
    "tokenErrorRate1": 0.12119999999999999,
    "sentenceCount1": 3,
    "wordCount1": 173,
    "correctWordCount1": 170,
    "wordSubstitutionCount1": 2,
    "wordDeletionCount1": 1,
    "wordInsertionCount1": 2,
    "tokenCount1": 165,
    "correctTokenCount1": 145,
    "tokenSubstitutionCount1": 10,
    "tokenDeletionCount1": 1,
    "tokenInsertionCount1": 9,
    "tokenErrors1": {
      "punctuation": {
        "numberOfEdits": 4,
        "percentageOfAllEdits": 20.0
      },
      "capitalization": {
        "numberOfEdits": 2,
        "percentageOfAllEdits": 10.0
      },
      "inverseTextNormalization": {
        "numberOfEdits": 1,
        "percentageOfAllEdits": 5.0
      },
      "lexical": {
        "numberOfEdits": 12,
        "percentageOfAllEdits": 12.0
      },
      "others": {
        "numberOfEdits": 1,
        "percentageOfAllEdits": 5.0
      }
    },
    "wordErrorRate2": 0.028900000000000002,
    "sentenceErrorRate2": 0.667,
    "tokenErrorRate2": 0.12119999999999999,
    "sentenceCount2": 3,
    "wordCount2": 173,
    "correctWordCount2": 170,
    "wordSubstitutionCount2": 2,
    "wordDeletionCount2": 1,
    "wordInsertionCount2": 2,
    "tokenCount2": 165,
    "correctTokenCount2": 145,
    "tokenSubstitutionCount2": 10,
    "tokenDeletionCount2": 1,
    "tokenInsertionCount2": 9,
    "tokenErrors2": {
      "punctuation": {
        "numberOfEdits": 4,
        "percentageOfAllEdits": 20.0
      },
      "capitalization": {
        "numberOfEdits": 2,
        "percentageOfAllEdits": 10.0
      },
      "inverseTextNormalization": {
        "numberOfEdits": 1,
        "percentageOfAllEdits": 5.0
      },
      "lexical": {
        "numberOfEdits": 12,
        "percentageOfAllEdits": 12.0
      },
      "others": {
        "numberOfEdits": 1,
        "percentageOfAllEdits": 5.0
      }
    }
  },
  "lastActionDateTime": "2024-07-14T21:22:45Z",
  "status": "Succeeded",
  "createdDateTime": "2024-07-14T21:21:39Z",
  "locale": "en-US",
  "displayName": "My Inspection",
  "description": "My Inspection Description"
}
```

::: zone-end

## Compare transcription with audio

You can inspect the transcription output by each model tested, against the audio input dataset. If you included two models in the test, you can compare their transcription quality side by side. 

::: zone pivot="ai-foundry-portal"

::: zone-end

::: zone pivot="speech-studio"

To review the quality of transcriptions:

1. Sign in to the [Speech Studio](https://aka.ms/speechstudio/customspeech).
1. Select **Custom speech** > Your project name > **Test models**.
1. Select the link by test name.
1. Play an audio file while the reading the corresponding transcription by a model. 

If the test dataset included multiple audio files, you see multiple rows in the table. If you included two models in the test,  transcriptions are shown in side-by-side columns. Transcription differences between models are shown in blue text font. 

:::image type="content" source="media/custom-speech/custom-speech-inspect-compare.png" alt-text="Screenshot of comparing transcriptions by two models":::

::: zone-end

::: zone pivot="speech-cli"

The audio test dataset, transcriptions, and models tested are returned in the [test results](#get-test-results). If only one model was tested, the `model1` value matches `model2`, and the `transcription1` value matches `transcription2`. 

To review the quality of transcriptions:
1. Download the audio test dataset, unless you already have a copy.
1. Download the output transcriptions.
1. Play an audio file while the reading the corresponding transcription by a model. 

If you're comparing quality between two models, pay particular attention to differences between each model's transcriptions. 

::: zone-end

::: zone pivot="rest-api"


The audio test dataset, transcriptions, and models tested are returned in the [test results](#get-test-results). If only one model was tested, the `model1` value matches `model2`, and the `transcription1` value matches `transcription2`. 

To review the quality of transcriptions:
1. Download the audio test dataset, unless you already have a copy.
1. Download the output transcriptions.
1. Play an audio file while the reading the corresponding transcription by a model. 

If you're comparing quality between two models, pay particular attention to differences between each model's transcriptions. 

::: zone-end

## Next steps

- [Test model quantitatively](how-to-custom-speech-evaluate-data.md)
- [Train your model](how-to-custom-speech-train-model.md)
- [Deploy your model](./how-to-custom-speech-train-model.md)

---
title: "Quickstart: Build an image classification model with the Custom Vision portal"
titleSuffix: Azure AI services
description: Learn how to use the Custom Vision web portal to create, train, and test an image classification model.
author: PatrickFarley
manager: nitinme
ms.service: azure-ai-custom-vision
ms.topic: quickstart
ms.date: 03/26/2025
ms.author: pafarley
ms.custom: mode-other
keywords: image recognition, image recognition app, custom vision
---

# Quickstart: Build an image classification model with the Custom Vision portal

This quickstart explains how to use the Custom Vision web portal to create an image classification model. Once you build a model, you can test it with new images and eventually integrate it into your own image recognition app.

## Prerequisites

- An Azure subscription. You can [create a free account](https://azure.microsoft.com/pricing/purchase-options/azure-account?icid=ai-services).
- A set of images to train your classification model. You can use the set of [sample images](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) on GitHub. Or, you can choose your own images using the [following tips](#upload-and-tag-images).
- A [supported web browser](overview.md#supported-browsers).

## Create Custom Vision resources

[!INCLUDE [create-resources](includes/create-resources.md)]

## Create a new project

Navigate to the [Custom Vision web page](https://customvision.ai), and then sign in with the same account that you used to sign in to the Azure portal.

:::image type="content" source="media/browser-home.png" alt-text="Screenshot showing the Custom Vision sign-in page.":::

1. To create your first project, select **New Project**. The **Create new project** dialog box appears.

    :::image type="content" source="media/getting-started-build-a-classifier/new-project.png" alt-text="Screenshot of the new project dialog box with fields for name, description, and domains.":::

1. Enter a name and a description for the project. Then select your Custom Vision Training Resource. If your signed-in account is associated with an Azure account, the Resource dropdown displays all of your compatible Azure resources.

   > [!NOTE]
   > If no resource is available, confirm that you've signed into [customvision.ai](https://customvision.ai) with the same account that you used to sign in to the [Azure portal](https://portal.azure.com). Also confirm you've selected the same *Directory* in the Custom Vision website as the directory in the Azure portal where your Custom Vision resources are located. In both sites, you can select your directory from the dropdown account menu at the top right corner of the screen.

1. Select **Classification** under **Project Types**. Then, under **Classification Types**, choose either **Multilabel** or **Multiclass**, depending on your use case. Multilabel classification applies any number of your tags to an image (zero or more), while multiclass classification sorts images into single categories (every image you submit is sorted into the most likely tag). You can change the classification type later, if you want to.

1. Next, select one of the available domains. Each domain optimizes the model for specific types of images, as described in the following table. You can change the domain later if you wish.

    |Domain|Purpose|
    |---|---|
    |__Generic__| Optimized for a broad range of image classification tasks. If none of the other domains are appropriate, or you're unsure of which domain to choose, select the Generic domain. |
    |__Food__|Optimized for photographs of dishes as you would see them on a restaurant menu. If you want to classify photographs of individual fruits or vegetables, use the Food domain.|
    |__Landmarks__|Optimized for recognizable landmarks, both natural and artificial. This domain works best when the landmark is clearly visible in the photograph. This domain works even if the landmark is slightly obstructed by people in front of it.|
    |__Retail__|Optimized for images that are found in a shopping catalog or shopping website. If you want high precision classifying between dresses, pants, and shirts, use this domain.|
    |__Compact domains__| Optimized for the constraints of real-time classification on mobile devices. The models generated by compact domains can be exported to run locally.|

1. Finally, select __Create project__.

## Choose training images

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## Upload and tag images

You can upload and manually tag images to help train the classifier. 

1. To add images, select __Add images__ and then select __Browse local files__. Select __Open__ to move to tagging. Your tag selection is applied to the entire group of images you upload, so it's easier to upload images in separate groups according to their applied tags. You can also change the tags for individual images after they're uploaded.

    :::image type="content" source="media/getting-started-build-a-classifier/add-images-1.png" alt-text="Screenshot of the Add Images control is shown in the upper left, and as a button at bottom center.":::

1. To create a tag, enter text in the __My Tags__ field and press Enter. If the tag already exists, it appears in a dropdown menu. In a multilabel project, you can add more than one tag to your images, but in a multiclass project you can add only one. To finish uploading the images, use the __Upload [number] files__ button. 

    :::image type="content" source="media/getting-started-build-a-classifier/add-images-3.png" alt-text="Screenshot of the image upload page with a field to add tags.":::

1. Select __Done__ once the images are uploaded.

    :::image type="content" source="media/getting-started-build-a-classifier/add-images-4.png" alt-text="Screenshot of the progress bar showing all tasks completed.":::

To upload another set of images, return to the top of this section and repeat the steps.

## Train the classifier

To train the classifier, select the **Train** button. The classifier uses all of the current images to create a model that identifies the visual qualities of each tag. This process can take several minutes.

:::image type="content" source="media/getting-started-build-a-classifier/train-1.png" alt-text="Screenshot of the train button in the top right of the web page's header toolbar.":::

The training process should only take a few minutes. During this time, information about the training process is displayed in the **Performance** tab.

:::image type="content" source="media/getting-started-build-a-classifier/train-2.png" alt-text="Screenshot of the browser window with training details in the main section.":::

## Evaluate the classifier

After training is complete, the model's performance is estimated and displayed. The Custom Vision Service uses the images that you submitted for training to calculate precision and recall. Precision and recall are two different measurements of the effectiveness of a classifier:

- **Precision** indicates the fraction of identified classifications that were correct. For example, if the model identified 100 images as dogs, and 99 of them were actually of dogs, then the precision would be 99%.
- **Recall** indicates the fraction of actual classifications that were correctly identified. For example, if there were actually 100 images of apples, and the model identified 80 as apples, the recall would be 80%.

:::image type="content" source="media/getting-started-build-a-classifier/train-3.png" alt-text="Screenshot of the training results showing the overall precision and recall, and the precision and recall for each tag in the classifier.":::

### Probability threshold

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## Manage training iterations

Each time you train your classifier, you create a new _iteration_ with updated performance metrics. You can view all of your iterations in the left pane of the **Performance** tab. You'll also find the **Delete** button, which you can use to delete an iteration if it's obsolete. When you delete an iteration, you delete any images that are uniquely associated with it.

To learn how to access your trained models programmatically, see [Call the prediction API](./use-prediction-api.md).

## Next step

In this quickstart, you learned how to create and train an image classification model using the Custom Vision web portal. Next, get more information on the iterative process of improving your model.

> [!div class="nextstepaction"]
> [Test and retrain a model](test-your-model.md)

* [What is Custom Vision?](./overview.md)

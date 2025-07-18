---
title: Content Understanding Glossary
titleSuffix: Azure AI services
description: Quick reference, detailed description on Content Understanding Terms and Definition
author: laujan
ms.author: lajanuar
manager: nitinme
ms.date: 05/19/2025
ms.service: azure-ai-content-understanding
ms.topic: conceptual
ms.custom:
  - build-2025
---

# Azure AI Content Understanding terminologies

| Term | Description |
|:---------|:----------|
| **File** | Any type of data, including text, documents, images, videos, and audio. |
| **File type** | The MIME type of a file, such as text/plain, application/pdf, image/jpeg, audio/wav, and video/mp4. Generic categories like *document* refer to all corresponding MIME types supported by the service. |
| **Analyzer** | A component that processes and extracts content and structured fields from files. Content Understanding offers a few analyzer templates for common scenarios. |
| **Analyzer template** | A predefined configuration and field schema for an analyzer. It simplifies creating analyzers by allowing modifications to a template instead of starting from scratch. This feature is available only in [Azure AI Foundry portal](https://ai.azure.com/?cid=learnDocs), not via REST API/SDKs. |
| **Analyzer result** | The output generated by an analyzer after processing input data. It typically includes extracted content in Markdown, extracted fields, and optional modality-specific details. |
| **Add-ons** | Added features that enhance content extraction results, such as layout elements, barcodes, and figures in documents. |
| **Fields** | List of structured key-value pairs derived from the content, as defined by the field schema. [Learn more about supported field value types.](service-limits.md#field-schema-limits) |
| **Field schema** | A formal description of the fields to extract from the input. It specifies the name, description, value type, generation method, and more for each field. |
| **Generation method** | The process of determining the extracted value of a specified field. Content Understanding supports: <br/> &bullet; **Extract**: Directly extract values from the input content, such as dates from receipts or item details from invoices. <br/> &bullet; **Classify**: Classify content into predefined categories, such as call sentiment or chart type. <br/> &bullet; **Generate**: Generate values from input data, such as summarizing an audio conversation or generating scene descriptions from videos. |
| **Span** | A reference indicating the location of an element (for example, field, word) within the extracted Markdown content. A character offset and length represent a span. Different programming languages use various character encodings, which can affect the exact offset and length values for Unicode text. To avoid confusion, spans are only returned if the desired encoding is explicitly specified in the request. Some elements can map to multiple spans if they aren't contiguous in the markdown (for example, page). |
| **Processing Location** | An API request parameter that defines the geographic region where Azure AI Services analyzes your data. You can choose from three options: `geography`, `dataZone`, and `global` to control where processing occurs. This setting helps meet data residency requirements and optimize performance or scalability based on your needs.
| **Grounding source** | The specific regions in content where a value was generated. It has different representations depending on the file type: <br>&bullet; **Image** - A polygon in the image, often an axis-aligned rectangle (bounding box). <br>&bullet; **PDF/TIFF** - A polygon on a specific page, often a quadrilateral. <br>&bullet; **Audio** - A start and end time range. <br>&bullet; **Video** - A start and end time range with an optional polygon in each frame, often a bounding box.|
| **Person directory** | A structured way to store face data for recognition tasks. You can add individual faces to the directory and later search for visually similar faces. You can also create person profiles, associate faces to them, and match new face images to known individuals. This setup supports both flexible face matching and identity recognition across images and videos. |
| **Confidence score** | The level of certainty that the extracted data is accurate. |
| **Category** | A distinct class within a classifier used to group similar input files based on shared characteristics or features. |

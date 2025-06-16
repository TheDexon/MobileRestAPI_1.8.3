# PepsiCo iSales Frontend API Documentation (Version 1.8)

This documentation describes the PepsiCo iSales Frontend API, designed for integration with the Image Recognition (IR) system. The API enables management of visits to points of sale, scenes, images, and reports related to product recognition on shelves. All technical terms, fields, and examples are provided in English, as in the original document, to align with development standards.

## Table of Contents

1. [Introduction](#introduction)  
   1.1 [Logical Objects](#logical-objects)  
   1.2 [Objects Relation](#objects-relation)  
   1.3 [Interaction Concept](#interaction-concept)  
       1.3.1 [Online Visit General Scheme](#online-visit-general-scheme)  
       1.3.2 [Offline Visit General Scheme](#offline-visit-general-scheme)  
       1.3.3 [General Actions](#general-actions)  

2. [Types Definition](#types-definition)  
   2.1 [ReportStatus Enumeration](#reportstatus-enumeration)  
   2.2 [SeriesType Enumeration](#seriestype-enumeration)  
   2.3 [SceneType Type](#scenetype-type)  
   2.4 [KeyValue Type](#keyvalue-type)  
   2.5 [Rectangle Type](#rectangle-type)  
   2.6 [Hint Type](#hint-type)  
   2.7 [SeriesDesc Type](#seriesdesc-type)  
   2.8 [ImageUnit Type](#imageunit-type)  
   2.9 [ReportType Type](#reporttype-type)  
   2.10 [SFA Metadata](#sfa-metadata)  
   2.11 [Image Metadata](#image-metadata)  

---

## Introduction

The PepsiCo iSales Frontend API is designed to integrate with the Image Recognition (IR) system, providing point-of-sale visit management, image processing, and product recognition reports. The main goal is to automate the accounting of products on shelves and monitor the performance of tasks by agents in stores.

### Logical Objects

Logical objects represent the key entities used in the API:

- **Visit**: Represents all activities performed at a point of sale during a real visit to a customer.  
- **Scene Type (Task ID)**: Characterizes a specific location in the point of sale with a combination of reports, actions, and KPIs.  
- **Scene**: An entity of a particular scene type that defines the physical scene during a visit. Each scene is associated with one Scene Type, which defines the list of reports, actions, and KPIs. A visit can contain multiple scenes of the same or different Scene Types. Scenes include photos linked to the scene and reports based on recognition results.  
- **Photo**: A single image uploaded to the image recognition system. Each image has an internal and/or external ID used for recognition or other actions.  
- **Photos Series**: Represents the order of photos captured according to defined rules.  
- **Request**: An image recognition request for a set of images in one scene.  
- **Report Type**: Describes a set of characteristics obtained from recognition, including KPIs, service information, error details, or analytics.  
- **Report**: The result of a Report Type for scene recognition in a specific scene.

### Objects Relation

![Objects Relation Diagram](images/Objects_relation.png)

### Interaction Concept

The API supports two main interaction scenarios.

#### Online Visit General Scheme

![Online Visit General Scheme](images/Online_visit_general_scheme.png)  

#### Offline Visit General Scheme

![Offline Visit General Scheme](images/Offline_visit_general_scheme.png)

#### General Actions

The API provides the following general actions for managing visits and recognition:

| Category                                    | Action                | Description                                              |
|---------------------------------------------|-----------------------|----------------------------------------------------------|
| **Recognition**                             | prepare_scene         | Early preparation scene and visit if it is applicable.   |
|                                             | image_upload          | Upload image to IR.                                      |
|                                             | image_delete          | Delete image from IR.                                    |
|                                             | recognize             | Request of recognition uploaded images.                  |
|                                             | finalize_visit        | Finalizing the visit in the shop.                        |
| **Status of Recognition**                   | report_list           | Get list of results of recognition with status.          |
| **Manipulation with Result of Recognition** | report_data           | Get result(report) of recognition.                       |
|                                             | image_download        | Download images from IR backend.                         |
| **Interactive Operations with Results (Reports)** | operation_result | Back user interaction result to IR backend.              |
|                                             | reference_description | Get extra description of report element.                 |
| **Services**                                | sync_ids              | IDs synchronization between SAF and IR.                  |
|                                             | api_version           | Return supported version by IR provider, minimum version 1.5. |
| **History**                                 | history_visitsids     | Get last consolidated visits history by IDs.             |
|                                             | history_visits        | Get last consolidated visits history by parameters.      |

---


## Types Definition
### ReportStatus Enumeration

Defines the status of reports on the server side.

| Code      | Description                                                                 |
|-----------|-----------------------------------------------------------------------------|
| READY     | Report fully ready and can be downloaded.                                   |
| NOTREADY  | Report not yet ready, report data will be empty. To refresh report status needed to request server again. |
| ERROR     | Faced some error during recognize.                                          |

### SeriesType Enumeration

Represents the type of photo series, to be agreed with the IR provider.

| Code                      | Description                              |
|---------------------------|------------------------------------------|
| will be agreed with IR provider | Type of sequence (Down-Up-Left, Snaking and etc). |

**Note**: Specific values for `SeriesType` are to be defined in collaboration with the IR provider.

### SceneType Type

Represents the type of scene, agreed with the IR provider. Type is a string enumeration stored in the SFA database.

| Field | Type   | Required | Description                                              |
|-------|--------|----------|----------------------------------------------------------|
| type  | String | Yes      | Enumeration will be agreed with IR provider. Type of scene (Main shelf, Cold zone, Cooler and etc). |
| name  | String | No       | Name of scene.                                           |

### KeyValue Type

Represents a key-value pair in JSON format.

| Field | Type   | Required | Description                     | Example                     |
|-------|--------|----------|---------------------------------|-----------------------------|
| key   | String | Yes      | Key identifier.                 | "shopid"                    |
| value | String | Yes      | Value associated with the key.  | "i29"                       |

**Example json**:
```json
{
  "key": "shopid",
  "value": "i29"
}
```

### Rectangle Type

Represents a rectangular area on an image.

| Field  | Type   | Required | Description                     |
|--------|--------|----------|---------------------------------|
| top    | String | Yes      | Offset relevant to topleft coner. |
| left   | String | Yes      | Offset relevant to topleft coner. |
| bottom | String | Yes      | Offset relevant to topleft coner. |
| right  | String | Yes      | Offset relevant to topleft coner. |

### Hint Type

Represents a notification or hint message.

| Field   | Type   | Required | Description                     |
|---------|--------|----------|---------------------------------|
| type    | String | Yes      | Notification type (ERR/WARNING/OK). |
| message | String | Yes      | Hint message.                   |

### SeriesDesc Type

Describes a series of photos for a particular image.

| Field  | Type        | Required | Description                                              |
|--------|-------------|----------|----------------------------------------------------------|
| key    | String      | Yes      | Series key (stitching image).                            |
| line   | String      | Yes      | Line number of photo relatively to the first photo.      |
| colmn  | String      | Yes      | Column number of photo relatively to the first photo.    |
| type   | SeriesType  | Yes      | Series type.                                             |

### ImageUnit Type

Represents an image captured in the user interface.

| Field        | Type   | Required | Description                                              |
|--------------|--------|----------|----------------------------------------------------------|
| ext_image_id | String | Yes      | External image ID.                                       |
| datafile     | String | Yes      | Jpeg image (format will be agreed with IR provider).     |

### ReportType Type

Represents the code of a recognition report, defining what must be recognized.

| Field | Type          | Required | Description                                              |
|-------|---------------|----------|----------------------------------------------------------|
| code  | String        | Yes      | Const value (values are agreed between SFA and IR provider). |
| flags | Array[String] | No       | Report flags (WEBSTYLE/JSONSTYLE/MIXSTYLE).              |

**Report Flags**:

| Flag      | Description                                              |
|-----------|----------------------------------------------------------|
| WEBSTYLE  | Report to be shown (in html format) and can be processed by end-user in SFA. |
| JSONSTYLE | Report should be processed by SFA.                       |
| MIXSTYLE  | Reserved.                                                |

### SFA Metadata

Metadata for a visit in the SFA system.

| Field                     | Type           | Required | Description                     | Example                                          |
|---------------------------|----------------|----------|---------------------------------|--------------------------------------------------|
| route                     | String         | Yes      | Route number.                   | "RUNOGD"                                         |
| shopid                    | String         | Yes      | Shop ID.                        | "0200231621"                                     |
| agentid                   | String         | Yes      | Agent ID.                       | "80322598"                                       |
| role                      | String         | Yes      | Agent role.                     | "Merch+"                                         |
| orgid                     | String         | Yes      | Organization ID.                | "1"                                              |
| scene_additional_attributes | Array[KeyValue] | No      | Additional scene attributes.    | [{"key": "0", "value": "test_l1_l1_l1"}, {"key": "1", "value": "test_l1_l1_id1"}, {"key": "2", "value": "test_l3_id1"}] |
| scene_attribute           | Array[String]  | No       | Scene attributes.               | ["AEP", "(1)", "Снеки", "Колонна", "Lays", "G-110"] |
| flag_after                | Integer (0-2)  | No       | Completion flag.                | "0"                                              |

**Example json**:
```json
{
  "route": "RUNOGD",
  "shopid": "0200231621",
  "agentid": "80322598",
  "role": "Merch+",
  "orgid": "1",
  "scene_additional_attributes": [
    {"key": "0", "value": "test_l1_l1_l1"},
    {"key": "1", "value": "test_l1_l1_id1"},
    {"key": "2", "value": "test_l3_id1"}
  ],
  "scene_attribute": ["AEP", "(1)", "Снеки", "Колонна", "Lays", "G-110"],
  "flag_after": "0"
}
```

### Image Metadata

Metadata describing the shooting conditions of an image.

| Field      | Type   | Required | Description                     | Example                     |
|------------|--------|----------|---------------------------------|-----------------------------|
| longitude  | String | Yes      | Longitude of capture location.  | "82,9467941"                |
| latitude   | String | Yes      | Latitude of capture location.   | "54,9603282"                |
| brightness | String | Yes      | Image brightness.               | "87"                        |
| contrast   | String | Yes      | Image contrast.                 | "241,527628537085"          |
| sword      | String | Yes      | (Possibly gyro roll angle).     | "-0,9965955"                |
| gyropitch  | String | Yes      | Gyro pitch angle.               | "5,985871"                  |

**Example json**:
```json
{
  "longitude": "82,9467941",
  "latitude": "54,9603282",
  "brightness": "87",
  "contrast": "241,527628537085",
  "sword": "-0,9965955",
  "gyropitch": "5,985871"
}
```

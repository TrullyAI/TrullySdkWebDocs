# Introduction

TrullyWebSDK is an identity validation component designed to be integrated into
your decision-making process.

⚠️ The component only works under the **HTTPS** protocol <br> ⚠️ For develop,
you should pass isDev true. See [<b>How to use</b>](#how-to-use) section <br> ⚠️
This is a client-side component. If your project was created with Next.js,
follow the steps in the [<b>Add it to Next.js</b>](#add-it-next) section

## Table of Contents

1. [Install](#install)
   1. [Add styles](#add-styles)
2. [How to use](#how-to-use)
3. [Tracking process](#tracking-process)
4. [Combine with other Decision Maker analysis](#combine-with-other-decision-maker-analysis)
5. [Callbacks data structure](#callbacks-data-structure)
   1. [handleTrack](#handletrack)
   2. [handleTrackDetail](#handletrackdetail)
   3. [handleData](#handledata)
   4. [handleError](#handleerror)
6. [Personalize](#personalize)
   1. [pagesToInclude key](#pagestoinclude-key)
   2. [inputs key](#inputs-key)
7. [Add it to Next.js](#add-it-next)
   1. [Next.js App Router System](#nextjs-app-router-system)
      1. [Add styles](#add-styles-1)
      2. [Creating a client-side component](#creating-a-client-side-component)
      3. [Dynamically import the component](#dynamically-import-the-component)
   2. [Next.js Page Router System](#nextjs-page-router-system)
      1. [Add styles](#add-styles-2)
      2. [Creating a component](#creating-a-component)
      3. [Dynamically import the component](#dynamically-import-the-component-1)
8. [Data handling](#data-handling)
   1. [handleData](#handledata-1)
   2. [Receiving data with webhook](#receiving-data-with-webhook)
9. [handleError](#handleerror-1)
10. [Full Example](#full-example)
11. [Known issues](#known-issues)

#### ⚠️ Before start using the TrullySdkWeb component we need to set some data on our server. Please contact us to ask for this set up. <br> We are going to need the following information

|                     | Description                                                                                                                                                | Example                                                                                      |
| ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| `logo`              | The url of your company logo to replace our                                                                                                                | https://trully-api-documentation.s3.us-east-1.amazonaws.com/trully-sdk/logo-trully-unico.svg |
| `Button color`      | Hexadecimal color code for the button background                                                                                                           | #475FFF                                                                                      |
| `Button color text` | Hexadecimal color code for the button text                                                                                                                 | #FFFFFF                                                                                      |
| `Domains`           | Every domain url in which you are going to use the TrullySdkWeb component. Make sure to let us know which are for production and which are for development | https://localhost:5374 - dev                                                                 |
|                     |                                                                                                                                                            | https://dominio.com - prod                                                                   |

## Install

```jsx
npm i @trully/trully-sdk-react
```

### Add styles

To import the styles, go to your main.js file and add the following import

```javascript
import "../node_modules/@trully/trully-sdk-react/dist/index.css";
```

## <a id="how-to-use"></a>How to use |

#### ⚠️ logo, button color and button color text are the only styles that can be replace

The component receives the "configuration" prop. This prop should be an object
containing the keys that will let you adapt it to your needs. There are the only
five required keys: "isDev", "apiKey", "user_id", "handleData" and
"handleError".

| Key                 | Description                                                                                                                        |
| ------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| `isDev`             | Set it to true when your working in development                                                                                    |
| `apiKey`            | Your cliente API Key. If isDev is true you should use the sandbox API Key. Otherwise, use the prod one                             |
| `user_id`           | String. Will match the response you get with the user completing the process                                                       |
| `handleData`        | Should be a callback function receiving a parameter. This parameter will hold the process result.                                  |
| `handleError`       | Should be a callback function receiving a parameter. This parameter will hold an error object.                                     |
| `handleTrack`       | (optional) Callback. Catch the step changing of the operation.                                                                     |
| `handleTrackDetail` | (optional) Callback. Catch the user's interaction during the operation.                                                            |
| `userIDIsCURP*`     | (optional) Boolean. If you're using the CURP as user_id and want to send it to the Decision Maker mark this as true. Default false |

\*curp input won't be showing in form page.

##### ⚠️ Please note that if you choose to use the user curp as user_id and the value is not a valid curp we won't be sending that value to the Decision Maker. It's very important that you verify the value before passing it to the SDK.

#### Example

```jsx
import { TrullySdkWeb } from "@trully/trully-sdk-react";

<TrullySdkWeb
  configuration={{
    isDev: true, // Change it to false in production
    apiKey: "YOUR_API_KEY", // Remember to use sandbox API key when isDev: true and prod API key when isDev: false
    user_id: "YOUR_USER_ID",
    handleData: (response) => {
      // What should be done with the obtained response?
    },
    handleError: (error) => {
      // What should be done if there is an error retrieving the response?
    },
  }}
/>;
```

## Combine with other Decision Maker analysis

TrullySdkWeb helps you to get a candidate data (by default you'll be collecting
document image, location and selfie) so our Decision Maker can analyze it. But
our Decision Maker could be used to analyze different data points and you may
want to combine every Decision Maker response you get for every data point
(learn more about Decision Maker on the
[API Docs](https://trully.readme.io/reference/decisionmakerpredict)). You can
join the response you get from the TrullySdkWeb with others Decision Maker
responses passing the request_id in the configuration object.

| Key          | Description                                                          |
| ------------ | -------------------------------------------------------------------- |
| `request_id` | String. The id generated by the Decision Maker for any analysis run. |

#### Example

```jsx
import { TrullySdkWeb } from "@trully/trully-sdk-react";

<TrullySdkWeb
  configuration={{
    isDev: true, // Change it to false in production
    apiKey: "YOUR_API_KEY", // Remember to use sandbox API key when isDev: true and prod API key when isDev: false
    user_id: "YOUR_USER_ID",
    handleData: (response) => {
      // What should be done with the obtained response?
    },
    handleError: (error) => {
      // What should be done if there is an error retrieving the response?
    },
    request_id: "REQUEST_ID_FOR_THE_ANALYSIS_OF_OTHER_DATA_POINT",
  }}
/>;
```

### Callbacks data structure

Here you'll found the structures of the data received in each callback function

#### handleTrack

This function will be called every time the user starts a new step on the
process. There are five different steps. When a new step is started, you'll
receive the data from the previous completed step.

| Key            | Description                          |
| -------------- | ------------------------------------ |
| `step`         | Name of the previous completed step. |
| `user_id`      | The user_id you passed in usage key. |
| `started_on`   | UTC timezone. Step starting time.    |
| `completed_on` | UTC timezone. Step completion time.  |

#### Steps Table

| Name                  | Description                                       |
| --------------------- | ------------------------------------------------- |
| `form_start`          | User is currently scanning the document.          |
| `form_document`       | Process is currently trying to obtained location. |
| `form_location`       | User is currently completing liveness process.    |
| `form_selfie`         | User reach the final step of the operation.       |
| `form_decision_maker` | Operation has the Decision Maker respond.         |

##### Example

```jsx
import { TrullySdkWeb } from "@trully/trully-sdk-react";

<TrullyWebSDK
  configuration={{
    // ...other required keys
    handleTrack: (step) => {
      console.log(step);
    },
  }}
/>;
```

#### handleTrackDetail

This function will be called when the user take some actions during the
operation. The actions that will trigger it are the ones related to the data
needed for the Decision Maker.

| Key         | Description                                   |
| ----------- | --------------------------------------------- |
| `user_id`   | The user_id you passed in usage key.          |
| `action`    | Name of the action that trigger the function. |
| `timestamp` | UTC timezone. When the function was trigger.  |

#### Actions Table

| Name                                     | Description                                            |
| ---------------------------------------- | ------------------------------------------------------ |
| `LEGAL_DOCUMENTATION_ACCEPTED`           | User accept Privacy Policy and Terms and Conditions.   |
| `LEGAL_DOCUMENTATION_DECLINED`           | User declined Privacy Policy and Terms and Conditions. |
| `DOCUMENT_IMAGES_OBTAINED`               | Operation has analyzed front and back image.           |
| `LOCATION_OBTAINED`                      | Operation has user's location.                         |
| `LOCATION_SKIPPED`                       | Operation has continued without user's location.       |
| `LIVENESS_RECOGNITION_PROCESS_COMPLETED` | Operation has analyzed liveness status.                |
| `END_KYC_PROCESS`                        | Operation has Decision Maker result.                   |

#### Example

```jsx
import { TrullySdkWeb } from "@trully/trully-sdk-react";

<TrullyWebSDK
  configuration={{
    // ...required keys
    handleTrackDetail: (trackDetail) => {
      console.log(trackDetail);
    },
  }}
/>;
```

### handleData

This function will be called when the operation gets the Decision Maker result.
Go to [<b>Data handling</b>](#results) section to get more information

### handleError

This function will be called in case of an error during the operation. You'll
receive the error object as is generated

We recommend you save this error into a log file.

| Key         | Description                                   |
| ----------- | --------------------------------------------- |
| `message`   | Error message                                 |
| `user_id`   | The user_id you passed during initialization. |
| `timestamp` | UTC timezone. When the function was trigger.  |

#### Example

```jsx
import { TrullySdkWeb } from "@trully/trully-sdk-react";

const handleError = (error) => {
  console.log("Error - message", error.message);
  console.log("Error - user_id", error.user_id);
  console.log("Error - timestamp", error.timestamp);
};

<TrullySdkWeb
  configuration={{
    isDev: true, // Change it to false in production
    apiKey: "YOUR_API_KEY", // Remember to use sandbox API key when isDev: true and prod API key when isDev: false
    user_id: "YOUR_USER_ID",
    handleData: (response) => {
      // What should be done with the obtained response?
    },
    handleError,
  }}
/>;
```

## Personalize

To customize the component, add the corresponding keys to the configuration
prop. Every one of these keys are optional

| Key                   | Description                                                    |
| --------------------- | -------------------------------------------------------------- |
| `termsAndConditions*` | String. URL to your terms and conditions page                  |
| `privacyPolicy*`      | String. URL to your privacy policy page                        |
| `pagesToInclude`      | Strings array. Will allow to add/remove pages from the process |

\*By default the component uses our terms and conditions and privacy policy
pages

#### Example

This example shows the default values for the `showLogo`, `termsAndConditions`
and `privacyPolicy` keys

```jsx
import { TrullySdkWeb } from "@trully/trully-sdk-react";

<TrullySdkWeb
  configuration={{
    // ...required keys
    termsAndConditions: "https://www.trully.ai/terminos-y-condiciones/",
    privacyPolicy: "https://www.trully.ai/politica-de-datos/",
  }}
/>;
```

### pagesToInclude key

With these key you'll be able to adapt the process to take some extra data from
your user by adding pages to the process. To add the pages you should pass an
array containing the following strings. Each string represents one page to
include.

| String          | Description                                                                                                   |
| --------------- | ------------------------------------------------------------------------------------------------------------- |
| `form`          | This will add a form to your process. The data collect with it can be select using the inputs key             |
| `personal_info` | This will add a form asking for personal info such as complete name, date of birth, place of birth and gender |

##### inputs key

If you include <b>form</b> page, inputs will let you choose which data will be
asked for. There are no inputs shown by default.

| Key            | Description                                                                                                                   |
| -------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| `email`        | Input asking for valid email address                                                                                          |
| `phone_number` | Input asking for valid mexican phone number                                                                                   |
| `curp`         | Input asking for valid curp. If you're using curp as user_id and passed true in userIDIsCURP key. This input won't be showing |
| `rfc`          | Input asking for valid rfc                                                                                                    |
| `address`      | Input asking for valid address                                                                                                |

#### Example

```jsx
import { TrullySdkWeb } from "@trully/trully-sdk-react";

<TrullyWebSDK
  configuration={{
    pagesToInclude: ["form"],
    inputs: {
      phone_number: true,
    },
  }}
/>;
```

## <a id="add-it-next"></a>Add it to Next.js

### Next.js App Router System

#### Add styles

To import the styles, go to your app level layout.js file (RootLayout Component)

and add the following import

```javascript
import "../node_modules/@trully/trully-react-components-npm/dist/index.css";
```

TrullySdkWeb is a client-side component, which means it needs to run on the
client. By default, every component created in Next.js is executed on the
server. You need to make sure our component runs only on the client.

#### Creating a client-side component

To ensure a component runs on the client, it should be marked as a client-side
component using the "use client" tag. In the Next.js project, create a new
component and mark it as a client-side component.

##### Example

```jsx
"use client";

import { TrullySdkWeb } from "@trully/trully-react-components-npm";

const Trully = () => {
  return (
    <TrullySdkWeb
      configuration={{
        isDev: true, // Change it to false in production
        apiKey: "YOUR_API_KEY", // Remember to use sandbox API key when isDev: true and prod API key when isDev: false
        user_id: "YOUR_USER_ID",
        handleData: (response) => {
          // What should be done with the obtained response?
        },
        handleError: (error) => {
          // What should be done if there is an error retrieving the response?
        },
      }}
    />
  );
};

export default Trully;
```

#### Dynamically import the component

Then, to ensure the component runs only on the client, make a dynamic import
with ssr: false.

##### Example

```jsx
import dynamic from "next/dynamic";

const DynamicComponent = dynamic(() => import("@/components/Trully"), {
  ssr: false,
});

export default function Home() {
  return <DynamicComponent />;
}
```

### Next.js Page Router System

#### Add styles

To import the styles, go to your \_app.js file and add the following import

```javascript
import "../node_modules/@trully/trully-react-components-npm/dist/index.css";
```

#### Creating a component

First, create a new component so you can configure TrullySdkWeb component

##### Example

```jsx
import { TrullySdkWeb } from "@trully/trully-react-components-npm";

const Trully = () => {
  return (
    <TrullySdkWeb
      configuration={{
        isDev: true, // Change it to false in production
        apiKey: "YOUR_API_KEY", // Remember to use sandbox API key when isDev: true and prod API key when isDev: false
        user_id: "YOUR_USER_ID",
        handleData: (data) => {
          // What should be done with the obtained data?
        },
        handleError: (error) => {
          // What should be done if there is an error retrieving the data?
        },
      }}
    />
  );
};

export default Trully;
```

#### Dynamically import the component

Then, to ensure the component runs only on the client, make a dynamic import
with ssr: false.

##### Example

```jsx
import dynamic from "next/dynamic";

const DynamicComponent = dynamic(() => import("@/components/Trully"), {
  ssr: false,
});

export default function Home() {
  return <DynamicComponent />;
}
```

## Data handling

TrullyWebSDK sends the obtained data to the
[API Decision Maker](https://docs.trully.ai/docs/about) to assist in your
decision-making process. Using this component, you can access the response data
from the Decision Maker and all the data collected during the KYC process. The
required "usage" key should have a "handleData" function and an "handleError"
function, both of which should receive a parameter. The "handleData" function
stores an object with the data processed by the Decision Maker and the data
obtained during the KYC process in its parameter. On the other hand, the
"handleError" function stores the error generated during the query. This way, we
can specify the actions to be taken when the server request is successful
(handleData) or if there is an error in the process (handleError).

### handleData

| Key                            | Description                                                                                                                                                      |
| ------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `tag`                          | String. The tag from the process. Automatically generated                                                                                                        |
| `user_id`                      | String. The user_id you passed in configuration                                                                                                                  |
| `raw_data`                     | Object containing the unprocessed data from the Decision Maker. You can learn more about [here](https://docs.trully.ai/reference/post_v1-decision-maker-predict) |
| `ip`                           | String. The ip of the device obtained during the process. Empty string if we couldn't get the ip                                                                 |
| `location`\*                   | Object. Keys lat/lng. The coordinates of the device obtained during the process. Keys lat/lng will be empty string if we couldn't get the location               |
| `calculated_rfc`               | String. The RFC calculated from the document. Could be undefined                                                                                                 |
| `label`                        | String. The label generate by the Decision Maker for the user who has completed the process                                                                      |
|                                | No Threat - low risk user. Review - medium risk user. Potential Threat - high risk                                                                               |
| `reason`                       | Array. Contains the reasons behind the decision                                                                                                                  |
| `request_id`                   | String. ID created by the Decision Maker                                                                                                                         |
| `image`                        | Base64 string. Selfie                                                                                                                                            |
| `document_image`               | Base64 string. Document front cropped                                                                                                                            |
| `document_image_complete`      | Base64 string. Document front uncropped                                                                                                                          |
| `document_image_back`          | Base64 string. Document back cropped                                                                                                                             |
| `document_image_back_complete` | Base64 string. Document back uncropped                                                                                                                           |

\*Given the way browsers works, there is a small chance the user location won't
be available if the user completes the process using a mobile device.

#### Receiving data with webhook

If you prefer it is possible to set a webhook url to receive the Decision Maker
response. To do it, just pass the key webhook to the configuration object

#### Example

```jsx
import { TrullySdkWeb } from "@trully/trully-react-components-npm";

const handleData = (response) => {
  console.log("Response - tag", response.tag);
  console.log("Response - user_id", response.user_id);
  console.log("Response - raw_data", response.raw_data);
  console.log("Response - label", response.label);
  console.log("Response - reason", response.reason);
  console.log("Response - image", response.image);
  console.log("Response - document_image", response.document_image);
  console.log(
    "Response - document_image_complete",
    response.document_image_complete
  );
  console.log("Response - document_image_back", response.document_image_back);
  console.log(
    "Response - document_image_back_complete",
    response.document_image_back_complete
  );
};

<TrullySdkWeb
  configuration={{
    isDev: true, // Change it to false in production
    apiKey: "YOUR_API_KEY", // Remember to use sandbox API key when isDev: true and prod API key when isDev: false
    user_id: "YOUR_USER_ID",
    handleData,
    handleError: (error) => {
      //What should be done if there is an error retrieving the response?
    },
    webhook: "VALID_WEBHOOK_URL",
  }}
/>;
```

## Full Example

Here you'll found an example to show you how to change every configuration
available. You can also see the
[Typescript Demo Implementation](https://github.com/TrullyAI/WebSDKTypescriptDemo)
or the
[Javascript Demo Implementation](https://github.com/TrullyAI/WebSDKJavascriptDemo)
to see how the SDK works

```jsx
import { TrullySdkWeb } from "@trully/trully-sdk-react";

const handleData = (response) => {
  console.log("Response - tag", response.tag);
  console.log("Response - user_id", response.user_id);
  console.log("Response - raw_data", response.raw_data);
  console.log("Response - label", response.label);
  console.log("Response - reason", response.reason);
  console.log("Response - image", response.image);
  console.log("Response - document_image", response.document_image);
  console.log(
    "Response - document_image_complete",
    response.document_image_complete
  );
  console.log("Response - document_back", response.document_back);
  console.log(
    "Response - document_back_complete",
    response.document_back_complete
  );
};

const handleError = (error) => {
  console.log("Error - name", error.name);
  console.log("Error - description", error.description);
  console.log("Error - error", error.error);
};

<TrullySdkWeb
  configuration={{
    isDev: true, // Change it to false in production
    apiKey: "YOUR_API_KEY", // Remember to use sandbox API key when isDev: true and prod API key when isDev: false
    user_id: "YOUR_USER_ID",
    handleData,
    handleError,
  }}
/>;
```

## Known issues

This section provides workarounds for some known issues that may occur while
using the component.

### Camera permission was accepted, but a message asking to accept it appears

This may happen for:

1. The component needs to work under the HTTPS protocol because the Browser
   Camera API will revoke access to the device if the connection is not secure.
   Make sure that you're working with HTTPS.
2. Some browsers will revoke access to the device while using auto-signed
   certificates. If you're working in development, you're probably creating an
   auto-signed certificate to force the HTTPS protocol. If that's the case, open
   your app in an incognito tab.
3. The Browser Camera API will automatically revoke permissions if there is
   another instance running. Make sure that you're only working in one tab at a
   time.
4. The Browser Camera API will automatically revoke permissions if the App has
   the Camera permissions denied. It's important to let the users know they'll
   have to change the App permissions to allow the Browser Camera API to ask for
   permissions

### There is no image from the Camera

This may happen for:

1. When the website hosting the SDK is opened via an integrated browser (i.e.,
   the browser from Messenger), the SDK may fail due to permission management
   issues. For this case, ask the user to copy the link and paste it directly to
   a browser (i.e., Chrome).

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
   2. [Prefetch the images/videos](#prefetch-the-imagesvideos)
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
   3. [Change images](#change-images)
   4. [Change videos](#change-videos)
   5. [Change texts](#change-texts)
   6. [Change styles](#change-styles)
      1. [Change texts styles](#textsStyles)
      2. [Change colors styles](#colors)
         1. [Change illustrations colors](#illustration_color)
7. [Add it to Next.js](#add-it-next)
   1. [Next.js App Router System](#nextjs-app-router-system)
      1. [Add styles](#add-styles-1)
      2. [Prefetch the images/videos](#prefetch-the-imagesvideos-1)
      3. [Creating a client-side component](#creating-a-client-side-component)
      4. [Dynamically import the component](#dynamically-import-the-component)
   2. [Next.js Page Router System](#nextjs-page-router-system)
      1. [Add styles](#add-styles-2)
      2. [Prefetch the images/videos](#prefetch-the-imagesvideos-2)
      3. [Creating a component](#creating-a-component)
      4. [Dynamically import the component](#dynamically-import-the-component-1)
8. [Data handling](#data-handling)
   1. [handleData](#handledata-1)
   2. [Receiving data with webhook](#receiving-data-with-webhook)
9. [handleError](#handleerror-1)
10. [Full Example](#full-example)
11. [Known issues](#known-issues)

## Install

```jsx
npm i @trully/trully-sdk-react
```

### Add styles

To import the styles, go to your main.js file and add the following import

```javascript
import "../node_modules/@trully/trully-react-components-npm/dist/index.css";
```

##### Prefetch the images/videos

Optionally, add the following link tag to the head of your your HTML to improve
the loading time of the images and videos

```html
<head>
  <!-- ...Rest of head tags -->
  <link
    rel="prefetch"
    href="https://trully-api-documentation.s3.amazonaws.com/trully-sdk/"
  />
</head>
```

## <a id="how-to-use"></a>How to use

The component receives the "configuration" prop. This prop should be an object
containing the keys that will let you adapt it to your needs. There are the only
five required keys: "isDev", "apiKey", "user_id", "handleData" and
"handleError".

| Key                 | Description                                                                                                                        |
| ------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| `isDev`             | Set it to true when your working in development                                                                                    |
| `apiKey`            | Your cliente API Key                                                                                                               |
| `user_id`           | String. Will match the response you get with the user completing the process                                                       |
| `handleData`        | Should be a callback function receiving a parameter. This parameter will hold the process result.                                  |
| `handleError`       | Should be a callback function receiving a parameter. This parameter will hold an error object.                                     |
| `handleTrack`       | (optional) Callback. Catch the step changing of the operation.                                                                     |
| `handleTrackDetail` | (optional) Callback. Catch the user's interaction during the operation.                                                            |
| `userIDIsCURP*`     | (optional) Boolean. If you're using the CURP as user_id and want to send it to the Decision Maker mark this as true. Default false |

\*curp input won't be showing in form page

#### Example

```jsx
import { TrullySdkWeb } from "@trully/trully-react-components-npm";

<TrullySdkWeb
  configuration={{
    isDev: true,
    apiKey: "YOUR_API_KEY",
    user_id: "YOUR_USER_ID",
    handleData: (response) => {
      //What should be done with the obtained response?
    },
    handleError: (error) => {
      //What should be done if there is an error retrieving the response?
    },
  }}
/>;
```

## Tracking process

You can declare a tag to track every process TrullySdkWeb has run. This key is
optional but we recommend using it from the start.

| Key   | Description                                                                      |
| ----- | -------------------------------------------------------------------------------- |
| `tag` | Valid uuid string. If you do not provide it, one will automatically generated.   |
|       | One analysis could run several process. Each one will be available with this tag |

#### Example

```jsx
import { TrullySdkWeb } from "@trully/trully-react-components-npm";

<TrullySdkWeb
  configuration={{
    isDev: true,
    apiKey: "YOUR_API_KEY",
    user_id: "YOUR_USER_ID",
    tag: "VALID_V4_UUID",
    handleData: (response) => {
      //What should be done with the obtained response?
    },
    handleError: (error) => {
      //What should be done if there is an error retrieving the response?
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
import { TrullySdkWeb } from "@trully/trully-react-components-npm";

<TrullySdkWeb
  configuration={{
    isDev: true,
    apiKey: "YOUR_API_KEY",
    user_id: "YOUR_USER_ID",
    tag: "VALID_V4_UUID",
    handleData: (response) => {
      //What should be done with the obtained response?
    },
    handleError: (error) => {
      //What should be done if there is an error retrieving the response?
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
import { TrullySdkWeb } from "@trully/trully-react-components-npm";

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
import { TrullySdkWeb } from "@trully/trully-react-components-npm";

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
import { TrullySdkWeb } from "@trully/trully-react-components-npm";

const handleError = (error) => {
  console.log("Error - message", error.message);
  console.log("Error - user_id", error.user_id);
  console.log("Error - timestamp", error.timestamp);
};

<TrullySdkWeb
  configuration={{
    isDev: true,
    apiKey: "YOUR_API_KEY",
    user_id: "YOUR_USER_ID",
    tag: "VALID_V4_UUID",
    handleData: (response) => {
      //What should be done with the obtained response?
    },
    handleError,
  }}
/>;
```

## Personalize

To customize the component, add the corresponding keys to the configuration
prop. Every one of these keys are optional

| Key                   | Description                                                                      |
| --------------------- | -------------------------------------------------------------------------------- |
| `showLogo`            | Set it to false to remove the logo image                                         |
| `termsAndConditions*` | String. URL to your terms and conditions page                                    |
| `privacyPolicy*`      | String. URL to your privacy policy page                                          |
| `pagesToInclude`      | Strings array. Will allow to add/remove pages from the process                   |
| `images`              | Object containing the images to replace the default ones                         |
| `videos`              | Object containing the videos to replace the default ones                         |
| `texts`               | Object. The keys from this object will modify some of the texts of the component |
| `styles`              | Object containing two optionals keys. textsStyles and colors keys                |

\*By default the component uses our terms and conditions and privacy policy
pages

#### Example

This example shows the default values for the `showLogo`, `termsAndConditions`
and `privacyPolicy` keys

```jsx
import { TrullySdkWeb } from "@trully/trully-react-components-npm";

<TrullySdkWeb
  configuration={{
    // ...required keys
    showLogo: true,
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
| `poa`           | This will Proof of Address process. Make sure your API Key includes this analysis                             |

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
import { TrullySdkWeb } from "@trully/trully-react-components-npm";

<TrullyWebSDK
  configuration={{
    pagesToInclude: ["form"],
    inputs: {
      phone_number: true,
    },
  }}
/>;
```

### Change images

Images are loaded using an image HTML tag, so to change it you should pass the
corresponding path to the image. Every one of these keys are optional. These are
the default values

| Key                | Description                                                                            |
| ------------------ | -------------------------------------------------------------------------------------- |
| `logo`             | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/logo-trully.svg           |
| `docIcon`          | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/ID-1.svg                  |
| `docOkIcon`        | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/icon_INEOK+color.svg      |
| `brightnessIcon`   | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/icon_Reflejo+color.svg    |
| `docFocusedIcon`   | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/icon_Ine+enfoque.svg      |
| `docUnfocusedIcon` | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/icon_blur+color.svg       |
| `permissions`      | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/ModalWeb.svg              |
| `iconCheck`        | https://trully-api-documentation.s3.us-east-1.amazonaws.com/trully-sdk/nuevo_Check.svg |
| `datosIcon`        | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/Datos-1.svg               |
| `IDIcon`           | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/ID-1.svg                  |
| `VideoIcon`        | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/Video-1.svg               |
| `IDImage`          | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/ID2-1.svg                 |
| `lightIcon`        | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/luzIcon.svg               |
| `crossIcon`        | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/retirarElementosIcon.svg  |
| `videoFallback`    | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/livenessFallback.svg      |
| `rotate`           | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/rotate.svg                |
| `poaReceipt`       | https://trully-api-documentation.s3.us-east-1.amazonaws.com/trully-sdk/Recibo.svg      |
| `poaDoc`           | https://trully-api-documentation.s3.us-east-1.amazonaws.com/trully-sdk/poaDoc.svg      |

#### Example

```jsx
import { TrullySdkWeb } from "@trully/trully-react-components-npm";

<TrullySdkWeb
  configuration={{
    // ...required keys
    images: {
        logo: "YOUR_LOGO_PATH,
        docIcon: "YOUR_IMAGE_PATH",
        docOkIcon: "YOUR_IMAGE_PATH",
        brightnessIcon: "YOUR_IMAGE_PATH",
        docFocusedIcon: "YOUR_IMAGE_PATH",
        docUnfocusedIcon: "YOUR_IMAGE_PATH",
        permissions: "YOUR_IMAGE_PATH",
        iconCheck: "YOUR_IMAGE_PATH",
        lightIcon: "YOUR_IMAGE_PATH",
        crossIcon: "YOUR_IMAGE_PATH",
        videoFallback: "YOUR_IMAGE_PATH",
        rotate: "YOUR_IMAGE_PATH",
        datosIcon: "YOUR_IMAGE_PATH",
        IDIcon: "YOUR_IMAGE_PATH",
        VideoIcon: "YOUR_IMAGE_PATH",
        IDImage: "YOUR_IMAGE_PATH",
        poaReceipt: "YOUR_IMAGE_PATH",
        poaDoc: "YOUR_IMAGE_PATH",
    },
  }}
/>;
```

#### Customize Logo CSS

Here is the CSS used for the logo so you can customize it if you need

```css
.trully-logo {
  position: relative;
  z-index: 10;
  top: 10px;
  width: 110px;
}
```

### Change videos

Videos are loaded using a video HTML tag, to change it you should pass the
corresponding path to the video. We recommend to change the videoFallback image
if you're going to change livenessVideo. videoFallback is use as livenessVideo
poster. Every one of these keys are optional. These are the default values.

| Key             | Description                                                                                                                          |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| `livenessVideo` | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/LivenessVideo.webm                                                      |
| `docFront`      | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/IneFront.webm                                                           |
| `docBack`       | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/IneBack.webm                                                            |
| `docFail`       | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/IneFail.webm                                                            |
| `poaIntro`      | https://trully-api-documentation.s3.us-east-1.amazonaws.com/trully-sdk/comprobante.webm - (only if you use Proof of Address process) |

#### Example

```jsx
import { TrullySdkWeb } from "@trully/trully-react-components-npm";

<TrullySdkWeb
  configuration={{
    // ...required keys
    videos: {
      livenessVideo: "YOUR_VIDEO_PATH",
      docFront: "YOUR_VIDEO_PATH",
      docBack: "YOUR_VIDEO_PATH",
      docFail: "YOUR_VIDEO_PATH",
      // only if you use Proof of Address process
      poaIntro: "YOUR_VIDEO_PATH",
    },
  }}
/>;
```

#### Customize CSS

Here is the CSS used for the videos so you can customize them if you need

```css
.trully-video-container {
  width: 55%;
  gap: 0;
}

.trully-video-position {
  padding: 0;
  position: relative;
  width: 100%;
  padding-top: 70%;
}

.true-deepfake-detection .trully-video-position {
  padding-top: 75%;
}

.trully-video-position video {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
}
```

##### Change individual video

The videos are inside individualize containers you can use to change the styles
of an specific video.

###### livenessVideo

```css
.true-deepfake-detection .trully-video-container {
  width: 55%;
  gap: 0;
}

.true-deepfake-detection .trully-video-position {
  padding: 0;
  position: relative;
  width: 100%;
  padding-top: 75%;
}

.true-deepfake-detection .trully-video-position video {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
}
```

###### docFront | docBack | docFail

```css
.trully-document-reader .trully-video-container {
  width: 55%;
  gap: 0;
}

.trully-document-reader .trully-video-position {
  padding: 0;
  position: relative;
  width: 100%;
  padding-top: 70%;
}

.trully-document-reader .trully-video-position video {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
}
```

###### poaIntro

```css
.trully-poa .trully-video-container {
  width: 55%;
  gap: 0;
}

.trully-poa .trully-video-position {
  padding: 0;
  position: relative;
  width: 100%;
  padding-top: 70%;
}

.trully-poa .trully-video-position video {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
}
```

### Change texts

You can change some texts by using the texts key. This key should receive an
object. Here is the structure. Each key is an object with the following optional
properties: title, subtitle, docType, warning and btnText. This properties will
be used accordingly.

| Key              | Description                                                                                                                                                   |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `appIntro`       | This page is used to introduce the process and ask for consent on terms and conditions and privacy policy                                                     |
| `form`           | This page will appear if you added "form" to pagesToInclude key. Will show the inputs added using inputs key                                                  |
| `personalInfo`   | This page will appear if you added "personal_info" to pagesToInclude key. Will show a form asking for complete name, date of birth, place of birth and gender |
| `documentReader` | This key represents the process of getting document images                                                                                                    |
| `liveness`       | This key represents the process of getting user selfie                                                                                                        |
| `poa`            | This key represents the Proof of Address process. It will appear if you added "poa" to pagesToInclude key. Make sure your API Key includes this analysis      |
| `exit`           | This page will thank the user and let them know we got the data                                                                                               |

#### Keys structure

Here you'll the default values for each an every text you could modify.

##### appIntro

| Key        | Description                            |
| ---------- | -------------------------------------- |
| `title`    | String. Vamos a verificar tu identidad |
| `subtitle` | String. Para este proceso necesitas:   |
| `btnText`  | String. Continuar                      |

##### form

| Key        | Description                                                                                                                                            |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `subtitle` | String. Completa este formulario con tu información personal veraz. Estamos comprometidos con la confidencialidad y seguridad de tus datos personales. |
| `btnText`  | String. Continuar                                                                                                                                      |

##### personalInfo

| Key        | Description                                                          |
| ---------- | -------------------------------------------------------------------- |
| `subtitle` | String. Escribe los datos como aparecen en tu identificación oficial |
| `btnText`  | String. Continuar                                                    |

##### documentReader

This key represents the process of getting document images. This process has
different pages. Those pages are represented by keys of the documentReader
object. Here is the structure of the document reader process.

| Key                 | Description                                                                         |
| ------------------- | ----------------------------------------------------------------------------------- |
| `docFrontIntro`     | This page will show the user how to take the picture to the front of the credential |
| `cameraPermissions` | This page will let the user know we will be asking for permission to use the camera |
| `docBackIntro`      | This page will show the user how to take the picture to the back of the credential  |

Each of the keys has the same logic you would use to modify the text of any
other page. Here are the default values you'll be changing

###### docFrontIntro

| Key       | Description                                         |
| --------- | --------------------------------------------------- |
| `title`   | String. Toma una foto de la parte frontal de tu INE |
| `btnText` | String. Tomar fotografía                            |

###### cameraPermissions

| Key        | Description                                                                                |
| ---------- | ------------------------------------------------------------------------------------------ |
| `title`    | String. Para continuar permite el acceso a la cámara                                       |
| `subtitle` | String. Cuando se le solicite, selecciona “Permitir” para habilitar el acceso a la cámara. |
| `btnText`  | String. Activar cámara                                                                     |

###### docBackIntro

| Key       | Description                                         |
| --------- | --------------------------------------------------- |
| `title`   | String. Toma una foto de la parte trasera de tu INE |
| `btnText` | String. Tomar fotografía                            |

##### liveness

This key represents the process of getting user selfie. This process has
different pages. Those pages are represented by keys of the liveness object.
Here is the structure of the liveness process.

| Key        | Description                                                                                             |
| ---------- | ------------------------------------------------------------------------------------------------------- |
| `appIntro` | This page will prompt the user to ready itself to take a selfie. Also, shows a photosensitivity warning |
| `retry`    | This page will appear wen the selfie process went wrong and it's necessary to do it again               |

Each of the keys has the same logic you would use to modify the text of any
other page. Here are the default values you'll be changing

###### appIntro

| Key       | Description                                                                                                        |
| --------- | ------------------------------------------------------------------------------------------------------------------ |
| `title`   | String. Ahora toma un video selfie                                                                                 |
| `warning` | String. La siguiente verificación contiene luces intermitentes que podrían afectar a personas con fotosensibilidad |
| `btnText` | String. Tomar video                                                                                                |

###### retry

| Key       | Description                |
| --------- | -------------------------- |
| `title`   | String. Inténtalo de nuevo |
| `btnText` | String. Reintentar         |

##### poa

This key represents the process of getting user Proof of Address. This process
has different pages. Those pages are represented by keys of the poa object. Here
is the structure of the poa process.

| Key        | Description                                                                |
| ---------- | -------------------------------------------------------------------------- |
| `appIntro` | This page will prompt the user to ready itself to take a the receipt photo |

Each of the keys has the same logic you would use to modify the text of any
other page. Here are the default values you'll be changing

###### appIntro

| Key       | Description                                                |
| --------- | ---------------------------------------------------------- |
| `title`   | String. Toma una fotografía de un comprobante de domicilio |
| `btnText` | String. Tomar fotografía                                   |

##### Exit

| Key        | Description                                   |
| ---------- | --------------------------------------------- |
| `title`    | String. ¡Gracias!                             |
| `subtitle` | String. Tu información fue recibida con éxito |

#### Example

```jsx
import { TrullySdkWeb } from "@trully/trully-react-components-npm";

<TrullyWebSDK
  configuration={{
    // ...required keys
    texts: {
      appIntro: {
        title: "YOUR_TEXT",
        subtitle: "YOUR_TEXT",
        btnText: "YOUR_TEXT",
      },
      form: {
        subtitle: "YOUR_TEXT",
        btnText: "YOUR_TEXT",
      },
      personalInfo: {
        subtitle: "YOUR_TEXT",
        btnText: "YOUR_TEXT",
      },
      documentReader: {
        docFrontIntro: {
          title: "YOUR_TEXT",
          btnText: "YOUR_TEXT",
        },
        cameraPermissions: {
          title: "YOUR_TEXT",
          subtitle: "YOUR_TEXT",
          btnText: "YOUR_TEXT",
        },
        docBackIntro: {
          title: "YOUR_TEXT",
          btnText: "YOUR_TEXT",
        },
      },
      liveness: {
        appIntro: {
          title: "YOUR_TEXT",
          warning: "YOUR_TEXT",
          btnText: "YOUR_TEXT",
        },
        retry: {
          title: "YOUR_TEXT",
          btnText: "YOUR_TEXT",
        },
      },
      poa: {
        appIntro: {
          title: "YOUR_TEXT",
          btnText: "YOUR_TEXT",
        },
      },
      exit: {
        title: "YOUR_TEXT",
        subtitle: "YOUR_TEXT",
      },
    },
  }}
/>;
```

### Change styles

The styles personalization is divided in sections

| Key           | Description                                        |
| ------------- | -------------------------------------------------- |
| `textsStyles` | Receives an object to declare font specific styles |
| `colors`      | Receives an object to declare colors styles        |

#### textsStyles

You can change the entire font using fonFamily or changes an specific font using
the individualFamily object. Every one of these keys are optional. These are the
default values.

| Key                  | Description                                  |
| -------------------- | -------------------------------------------- |
| `fontFamily`         | "DM Sans", sans-serif                        |
| `individualFamily`   | Object to declare font for specific elements |
| `primaryTextColor`   | #181c21 (titles)                             |
| `secondaryTextColor` | #181c21 (texts)                              |

###### Example

```jsx
import { TrullySdkWeb } from "@trully/trully-react-components-npm";

<TrullySdkWeb
  configuration={{
    // ...required keys
    styles: {
      textsStyles: {
        fontFamily: "YOUR_FONT",
        // Colors should be expressed in hexadecimal
        primaryTextColor: "YOUR_HEX_COLOR",
        secondaryTextColor: "YOUR_HEX_COLOR",
      },
    },
  }}
/>;
```

##### individualFamily

Every one of these keys are optional. If not specified will default to "DM
Sans", sans-serif

| Key               | Description            |
| ----------------- | ---------------------- |
| `text1BoldFamily` | Titles. Each bold text |
| `text1Family`     | Texts                  |

##### Example

```jsx
import { TrullySdkWeb } from "@trully/trully-react-components-npm";

<TrullySdkWeb
  configuration={{
    // ...required keys

    styles: {
      textsStyles: {
        individualFamily: {
          text1BoldFamily: "YOUR_FONT", //titles
          text1Family: "YOUR_FONT", //texts
        },
        // Colors should be expressed in hexadecimal
        primaryTextColor: "YOUR_HEX_COLOR",
        secondaryTextColor: "YOUR_HEX_COLOR",
      },
    },
  }}
/>;
```

#### colors

Colors will allow you change the different colors to match your brand design.
Every one of these keys are optional. These are the default values.

| Key                  | Description          |
| -------------------- | -------------------- |
| `primary`            | #475fff              |
| `secondary`          | #001063              |
| `disabled`           | #E5EBF3              |
| `white`              | #FFFFFF              |
| `background`         | #FFFFFF              |
| `icons`              | None                 |
| `illustration_color` | ILLUSTRATION.DEFAULT |

##### Example

```jsx
import { TrullySdkWeb } from "@trully/trully-react-components-npm";

<TrullySdkWeb
  configuration={{
    // ...required keys
    styles: {
      colors: {
        primary: "YOUR_HEX_COLOR",
        secondary: "YOUR_HEX_COLOR",
        disabled: "YOUR_HEX_COLOR",
        white: "YOUR_HEX_COLOR",
        background: "YOUR_HEX_COLOR",
        icons: "YOUR_HEX_COLOR",
        illustration_color: ILLUSTRATION.DEFAULT | ILLUSTRATION.MONO | ILLUSTRATION.ELECTRIC | ILLUSTRATION.3D
      },
    },
  }}
/>;
```

##### illustration_color

For the errors we use illustrations that could not be changed but you could
choose the color style for them. There are four color styles and you can choose
it using the ILLUSTRATION enum. Here are the values

| ILLUSTRATION |
| ------------ |
| `DEFAULT`    |
| `MONO`       |
| `ELECTRIC`   |
| `3D`         |

###### DEFAULT

| Key                        | Description                                                                                                   |
| -------------------------- | ------------------------------------------------------------------------------------------------------------- |
| `Timeout Cliente`          | https://trully-api-documentation.s3.us-east-1.amazonaws.com/illustrations/default/Advertencia_original.svg    |
| `Error API Key`            | https://trully-api-documentation.s3.us-east-1.amazonaws.com/illustrations/default/ApiKeyError_original.svg    |
| `Server Error`             | https://trully-api-documentation.s3.us-east-1.amazonaws.com/illustrations/default/Error_original.svg          |
| `Low Internet Speed`       | https://trully-api-documentation.s3.us-east-1.amazonaws.com/illustrations/default/RedBloqueada_original.svg   |
| `Camera Permission Denied` | https://trully-api-documentation.s3.us-east-1.amazonaws.com/illustrations/default/VideoBloqueado_original.svg |

###### MONO

| Key                        | Description                                                                                                     |
| -------------------------- | --------------------------------------------------------------------------------------------------------------- |
| `Timeout Cliente`          | https://trully-api-documentation.s3.us-east-1.amazonaws.com/illustrations/mono/Advertencia_monocromatico.svg    |
| `Error API Key`            | https://trully-api-documentation.s3.us-east-1.amazonaws.com/illustrations/mono/ApiKeyError_monocromatico.svg    |
| `Server Error`             | https://trully-api-documentation.s3.us-east-1.amazonaws.com/illustrations/mono/Error_monocromatico.svg          |
| `Low Internet Speed`       | https://trully-api-documentation.s3.us-east-1.amazonaws.com/illustrations/mono/RedBloqueada_monocromatico.svg   |
| `Camera Permission Denied` | https://trully-api-documentation.s3.us-east-1.amazonaws.com/illustrations/mono/VideoBloqueado_monocromatico.svg |

###### ELECTRIC

| Key                        | Description                                                                                                     |
| -------------------------- | --------------------------------------------------------------------------------------------------------------- |
| `Timeout Cliente`          | https://trully-api-documentation.s3.us-east-1.amazonaws.com/illustrations/electric/Advertencia_electrico.svg    |
| `Error API Key`            | https://trully-api-documentation.s3.us-east-1.amazonaws.com/illustrations/electric/ApiKeyError_electrico.svg    |
| `Server Error`             | https://trully-api-documentation.s3.us-east-1.amazonaws.com/illustrations/electric/Error_electrico.svg          |
| `Low Internet Speed`       | https://trully-api-documentation.s3.us-east-1.amazonaws.com/illustrations/electric/RedBloqueada_electrico.svg   |
| `Camera Permission Denied` | https://trully-api-documentation.s3.us-east-1.amazonaws.com/illustrations/electric/VideoBloqueado_electrico.svg |

###### 3D

| Key                        | Description                                                                                        |
| -------------------------- | -------------------------------------------------------------------------------------------------- |
| `Timeout Cliente`          | https://trully-api-documentation.s3.us-east-1.amazonaws.com/illustrations/3d/Advertencia_3d.svg    |
| `Error API Key`            | https://trully-api-documentation.s3.us-east-1.amazonaws.com/illustrations/3d/ApiKeyError_3d.svg    |
| `Server Error`             | https://trully-api-documentation.s3.us-east-1.amazonaws.com/illustrations/3d/Error_3d.svg          |
| `Low Internet Speed`       | https://trully-api-documentation.s3.us-east-1.amazonaws.com/illustrations/3d/RedBloqueada_3d.svg   |
| `Camera Permission Denied` | https://trully-api-documentation.s3.us-east-1.amazonaws.com/illustrations/3d/VideoBloqueado_3d.svg |

## <a id="add-it-next"></a>Add it to Next.js

### Next.js App Router System

#### Add styles

To import the styles, go to your app level layout.js file (RootLayout Component)

and add the following import

```javascript
import "../node_modules/@trully/trully-react-components-npm/dist/index.css";
```

##### Prefetch the images/videos

Optionally, add the following link tag to the head of your your HTML to improve
the loading time of the images and videos

```html
<head>
  <!-- ...Rest of head tags -->
  <link
    rel="prefetch"
    href="https://trully-api-documentation.s3.amazonaws.com/trully-sdk/"
  />
</head>
```

##### Example

```jsx
import { Inter } from "next/font/google";
import "./globals.css";
import "../node_modules/@trully/trully-react-components-npm/dist/index.css";

const inter = Inter({ subsets: ["latin"] });

export const metadata = {
  title: "Create Next App",
  description: "Generated by create next app",
};

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <head>
        <link
          rel="prefetch"
          href="https://trully-api-documentation.s3.amazonaws.com/trully-sdk/"
        />
      </head>
      <body className={inter.className}>{children}</body>
    </html>
  );
}
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
        isDev: true,
        apiKey: "YOUR_API_KEY",
        user_id: "YOUR_USER_ID",
        tag: "VALID_V4_UUID",
        handleData: (response) => {
          //What should be done with the obtained response?
        },
        handleError: (error) => {
          //What should be done if there is an error retrieving the response?
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

##### Prefetch the images/videos

Optionally, add the following link tag to the head of your your HTML to improve
the loading time of the images and videos

```html
<head>
  <!-- ...Rest of head tags -->
  <link
    rel="prefetch"
    href="https://trully-api-documentation.s3.amazonaws.com/trully-sdk/"
  />
</head>
```

##### Example

```jsx
import Head from "next/head";
import "../styles/globals.css";
import "../node_modules/@trully/trully-react-components-npm/dist/index.css";

function MyApp({ Component, pageProps }) {
  return (
    <>
      <Head>
        <link
          rel="prefetch"
          href="https://trully-api-documentation.s3.amazonaws.com/trully-sdk/"
        />
      </Head>
      <Component {...pageProps} />
    </>
  );
}

export default MyApp;
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
        isDev: true,
        apiKey: "YOUR_API_KEY",
        user_id: "YOUR_USER_ID",
        tag: "VALID_V4_UUID",
        handleData: (data) => {
          //What should be done with the obtained data?
        },
        handleError: (error) => {
          //What should be done if there is an error retrieving the data?
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
| `tag`                          | String. The tag from the process. Automatically generated when you didn't pass one through configuration prop                                                    |
| `user_id`                      | String. The user_id you passed in configuration                                                                                                                  |
| `raw_data`                     | Object containing the unprocessed data from the Decision Maker. You can learn more about [here](https://docs.trully.ai/reference/post_v1-decision-maker-predict) |
| `ip`                           | String. The ip of the device obtained during the process. Empty string if we couldn't get the ip                                                                 |
| `location`                     | Object. Keys lat/lng. The coordinates of the device obtained during the process. Keys lat/lng will be empty string if we couldn't get the location               |
| `label`                        | String. The label generate by the Decision Maker for the user who has completed the process                                                                      |
|                                | No Threat - low risk user. Review - medium risk user. Potential Threat - high risk                                                                               |
| `reason`                       | Array. Contains the reasons behind the decision                                                                                                                  |
| `request_id`                   | String. ID created by the Decision Maker                                                                                                                         |
| `image`                        | Base64 string. Selfie                                                                                                                                            |
| `document_image`               | Base64 string. Document front cropped                                                                                                                            |
| `document_image_complete`      | Base64 string. Document front uncropped                                                                                                                          |
| `document_image_back`          | Base64 string. Document back cropped                                                                                                                             |
| `document_image_back_complete` | Base64 string. Document back uncropped                                                                                                                           |
| `poa_image`                    | Base64 string. Proof Of Address receipt. Only if you have pagesToInclude: ["poa"]                                                                                |

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
    isDev: true,
    apiKey: "YOUR_API_KEY",
    user_id: "YOUR_USER_ID",
    tag: "VALID_V4_UUID",
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
    isDev: true,
    apiKey: "YOUR_API_KEY",
    user_id: "YOUR_USER_ID",
    tag: "VALID_V4_UUID",
    handleData,
    handleError,
    showLogo: true,
    images: {
      logo: "https://trully-api-documentation.s3.amazonaws.com/trully-sdk/logo-trully.svg",
      docIcon:
        "https://trully-api-documentation.s3.amazonaws.com/trully-sdk/ID-1.svg",
      docOkIcon:
        "https://trully-api-documentation.s3.amazonaws.com/trully-sdk/icon_INEOK+color.svg",
      brightnessIcon:
        "https://trully-api-documentation.s3.amazonaws.com/trully-sdk/icon_INEOK+color.svg",
      docFocusedIcon:
        "https://trully-api-documentation.s3.amazonaws.com/trully-sdk/icon_Ine+enfoque.svg",
      docUnfocusedIcon:
        "https://trully-api-documentation.s3.amazonaws.com/trully-sdk/icon_blur+color.svg",
      permissions:
        "https://trully-api-documentation.s3.amazonaws.com/trully-sdk/ModalWeb.svg",
      iconCheck:
        "https://trully-api-documentation.s3.amazonaws.com/trully-sdk/icon-check.svg",
      lightIcon:
        "https://trully-api-documentation.s3.amazonaws.com/trully-sdk/luzIcon.svg",
      crossIcon:
        "https://trully-api-documentation.s3.amazonaws.com/trully-sdk/retirarElementosIcon.svg",
      videoFallback:
        "https://trully-api-documentation.s3.amazonaws.com/trully-sdk/livenessFallback.svg",
      rotate:
        "https://trully-api-documentation.s3.amazonaws.com/trully-sdk/rotate.svg",
      datosIcon:
        "https://trully-api-documentation.s3.amazonaws.com/trully-sdk/Datos-1.svg",
      IDIcon:
        "https://trully-api-documentation.s3.amazonaws.com/trully-sdk/ID-1.svg",
      VideoIcon:
        "https://trully-api-documentation.s3.amazonaws.com/trully-sdk/Video-1.svg",
      IDImage:
        "https://trully-api-documentation.s3.amazonaws.com/trully-sdk/ID2-1.svg",
      poaReceipt:
        "https://trully-api-documentation.s3.us-east-1.amazonaws.com/trully-sdk/Recibo.svg",
      poaDoc:
        "https://trully-api-documentation.s3.us-east-1.amazonaws.com/trully-sdk/poaDoc.svg",
    },
    videos: {
      livenessVideo:
        "https://trully-api-documentation.s3.amazonaws.com/trully-sdk/LivenessVideo.webm",
      docFront:
        "https://trully-api-documentation.s3.amazonaws.com/trully-sdk/IneFront.webm",
      docBack:
        "https://trully-api-documentation.s3.amazonaws.com/trully-sdk/IneBack.webm",
      docFail:
        "https://trully-api-documentation.s3.amazonaws.com/trully-sdk/IneFail.webm",
      poaIntro:
        "https://trully-api-documentation.s3.us-east-1.amazonaws.com/trully-sdk/comprobante.webm",
    },
    texts: {
      appIntro: {
        title: "Vamos a verificar tu identidad",
        subtitle: "Para este proceso necesitas:",
        btnText: "Continuar",
      },
      // Add only if you have pagesToInclude: ["form"]
      form: {
        subtitle:
          "Completa este formulario con tu información personal veraz. Estamos comprometidos con la confidencialidad y seguridad de tus datos personales.",
        btnText: "Continuar",
      },
      // Add only if you have pagesToInclude: ["personal_info"]
      personalInfo: {
        subtitle:
          "Escribe los datos como aparecen en tu identificación oficial",
        btnText: "Continuar",
      },
      documentReader: {
        docFrontIntro: {
          title: "Toma una foto de la parte frontal de tu INE",
          btnText: "Tomar fotografía",
        },
        cameraPermissions: {
          title: "Para continuar permite el acceso a la cámara",
          subtitle:
            "Cuando se le solicite, selecciona “Permitir” para habilitar el acceso a la cámara.",
          btnText: "Activar cámara",
        },
        docBackIntro: {
          title: "Toma una foto de la parte trasera de tu INE",
          btnText: "Tomar fotografía",
        },
      },
      liveness: {
        appIntro: {
          title: "Ahora toma un video selfie",
          warning:
            "La siguiente verificación contiene luces intermitentes que podrían afectar a personas con fotosensibilidad",
          btnText: "Tomar video",
        },
        retry: {
          title: "Inténtalo de nuevo",
          btnText: "Reintentar",
        },
      },
      // Add only if you have pagesToInclude: ["poa"]
      poa: {
        appIntro: {
          title: "Toma una fotografía de un comprobante de domicilio",
          btnText: "Tomar fotografía",
        },
      },
      exit: {
        title: "¡Gracias!",
        subtitle: "Tu información fue recibida con éxito",
      },
    },
    styles: {
      textsStyles: {
        fontFamily: "'DM Sans', sans-serif",
        // Colors should be expressed in hexadecimal
        primaryTextColor: "#181c21", //titles
        secondaryTextColor: "#181c21", //texts
      },
      colors: {
        primary: "#475fff",
        secondary: "#001063",
        disabled: "#E5EBF3",
        white: "#FFFFFF",
        background: "#FFFFFF",
        icons: undefined,
        illustration_color: ILLUSTRATION.DEFAULT,
      },
    },
  }}
/>;
```

## Known issues

This section provides workarounds for some known issues that may occur while
using the component.

### Camera permission was accepted, but a message asking to accept it appears

This may happen for three reasons:

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

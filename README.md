# Bria iFrame
Welcome to the comprehensive guide on integrating BRIA's iFrame into your web applications. This cutting-edge tool is designed to revolutionize the way users interact with image generation technologies.
By embedding BRIA's iFrame, you can offer your users a seamless and intuitive interface for generating and customizing images with advanced AI capabilities. Our guide will walk you through the necessary steps to implement this innovative editor, ensuring a smooth integration process.

## Integration Process Overview
The process of integrating BRIA's iFrame into your web application consists of three main steps:

### Step 1: Create Your iFrame on BRIA's Platform
Begin by creating your iFrame on BRIA's platform. Here, you have the flexibility to customize the iFrame's features and user experience to align with the needs of your product's users. You can also tailor the iFrame's appearance to match your brand's style. Once you have configured the iFrame to your satisfaction, BRIA's platform will provide you with a unique URL. This URL is specifically generated for your iFrame to be embedded into your website. Use this URL in the `<iframe>` tag src attribute, check `index.html` file as an example.

### Step 2: Embed Your Unique BRIA's URL on Your Site
The next step involves embedding the unique URL provided by BRIA into your web application. This integration allows the iFrame to become a part of your site, offering a cohesive and integrated user experience.

### Step 3: Listen to Post Messages from the iFrame
Finally, it's essential to set up a mechanism to listen to post messages generated by the iFrame. This step is crucial for streamlining user actions into your own system. For example, you can capture and save customized images created by users in your own image repository. This integration ensures that user interactions with the iFrame are seamlessly incorporated into your web application's workflow.

By following these steps, you can successfully integrate BRIA's iFrame into your web application, enhancing its capabilities and offering a unique experience to your users.

## iFrame URL Query Parameters

| Query Parameter         | Details                                                                                                                                                                         | Is required?                                                                                        |
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| `iframeId`              | The ID of the iFrame                                                                                                                                                            | Yes                                                                                                 |
| `vhash`                 | BRIA's visual ID of the image to open inside the iFrame Playground page, you can use this if you already have the image registered into BRIA                                    | Either imageUrl or vhash is required in case the iFrame starting page is set to the Playground page |
| `imageUrl`              | The URL of the image to open inside the iFrame Playground page                                                                                                                  | Either imageUrl or vhash is required in case the iFrame starting page is set to the Playground page |
| `usageText`             | It is possible to add text that will appear in the header row in the Playground page (note: it is advised to keep the text as short as possible to fit the space in the header) | No                                                                                                  |
| `sourceDomain`          | Should pass the parent website host in order to make postMessages work, example: https://example.com                                                                            |                                                                                                     |
| `userId`                | The userId coming from the parent site, we return this value in the postMessages                                                                                                |                                                                                                     |
| `sessionId`             | The sessionId coming from the parent site, we return this value in the postMessages                                                                                             |                                                                                                     |
| `selectedTab`           | To open a specific tab in the playground, for example: presenters, size, etc.                                                                                                   |                                                                                                     |

## Running the Example
Follow the below steps to run the example:
1. Replace the example iFrame link in `index.html` with the link you got through BRIA's Platform
2. Build docker
    ```
    docker build -t bria-iframe .
    ```
2. Run docker
    ```
    docker run -d -p 80:80 --name bria-iframe-container bria-iframe
    ```
3. Open `localhost:80` using your preferable browser
4. One done, use the below commands to stop the docker container
    ```
    docker stop bria-iframe-container
    docker rm bria-iframe-container
    ```

## Post Messages
Set up a system to capture and process post messages from the iFrame, linking user interactions directly to your application.

### Listen to post messages using JS
```JAVASCRIPT
<script>
    window.addEventListener(
        "message",
        (event) => {
            if (event.data && event.data["type"] && event.data["type"].startsWith("Bria")) {
                console.log(event.data);
            }
        },
        false
    ); 
</script>
```

### Post Message Payload

Post message payload includes the following attributes as a JSON object:

```json
{
  "type": "The post message type, see the `Post Message Types` section below for more details",
  "message": "post message data, see the `Post Message Data Fields` section below for more details",
  "inputData": {
    "userId": "Represents the user ID passed when the iFrame was called for the first time in the session",
    "sessionId": "Represents the session ID passed when the iFrame was called for the first time in the session",
    "vhash": "VisualHash value (Bria's Image unique ID)",
    "...": "Other attributes that were fed into the iFrame when initiated in your web page"
  }
}
```

### Post Message Types:

| Post message type           | Triggered when                                                                           | Data provided         |
|-----------------------------|------------------------------------------------------------------------------------------|-----------------------|
| `Bria_ImageSave`            | The user clicks on the save image button                                                 | `imageUrl`, `changes` |
| `Bria_ImageSavePSD`         | The user clicks on the save PSD button                                                   | `imageUrl`, `changes` |
| `Bria_ImageDownload`        | The user clicks on the download image button                                             | `imageUrl`, `changes` |
| `Bria_ApplyChange`          | An image is manipulated in the Playground                                                | `type`, `action`      |
| `Bria_ApiAction`            | An API action is made                                                                    | `type`                |
| `Bria_InitialLoadComplete`  | iFrame initial page load is complete, can be used to show a loader on your web page      |                       |
| `Bria_GalleryImageOpen`     | The user clicks an image in the galley page                                              |                       |
| `Bria_GalleryOpen`          | The user opened the gallery page                                                         |                       |
| `Bria_IframePageNavigation` | The user navigated between iFrame pages                                                  | `page`, `path`        |
| `Bria_CloseClicked`         | The user clicked on the iFrame close button, in case you enabled the iFrame close button |                       |

### Post Message Data Fields

The below attributes are passed through the `message` field:
* `imageUrl`: The URL of the manipulated image
* `changes`: The changes applied to the manipulated image
* `type`: The type of the manipulation, can be: `expression`, `diversity`, `remove_object`, `increase_resolution`, `remove_background`, `Blur_background`, `Image_Style`, `replace_background`, `uncrop` or `presenters_style`
* `action`: Specific values for the manipulation, for example, the selected face `expression` manipulation value (`Happy`, `Sad`, etc.)
* `page`: The name of the navigated page, could be `gallery`, `playground`, etc.
* `path`: The path of the navigated page, could be `/gallery`, `/gallery/{vhash}`, etc.

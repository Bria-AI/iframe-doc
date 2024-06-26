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

## Running the Example
Follow the below steps to run the example:
1. Build docker
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

| Post message type         | Triggered when                                                                           | Data provided         |
|---------------------------|------------------------------------------------------------------------------------------|-----------------------|
| Bria_ImageSave            | The user clicks on the save image button                                                 | `imageUrl`, `changes` |
| Bria_ImageSavePSD         | The user clicks on the save PSD button                                                   | `imageUrl`, `changes` |
| Bria_ImageDownload        | The user clicks on the download image button                                             | `imageUrl`, `changes` |
| Bria_ApplyChange          | An image is manipulated in the Playground                                                | `type`, `action`      |
| Bria_InitialLoadComplete  | iFrame initial page load is complete, can be used to show a loader on your web page      |                       |
| Bria_GalleryImageOpen     | The user clicks an image in the galley page                                              |                       |
| Bria_GalleryOpen          | The user opened the gallery page                                                         |                       |
| Bria_IframePageNavigation | The user navigated between iFrame pages                                                  | `page`, `path`        |
| Bria_CloseClicked         | The user clicked on the iFrame close button, in case you enabled the iFrame close button |                       |

### Post Message Data Fields

The below attributes are passed through the `message` field:
* `imageUrl`: The URL of the manipulated image
* `changes`: The changes applied to the manipulated image
* `type`: The type of the manipulation, can be: `expression`, `diversity`, `remove_object`, `increase_resolution`, `remove_background`, `Blur_background`, `Image_Style`, `replace_background`, `uncrop` or `presenters_style`
* `action`: Specific values for the manipulation, for example, the selected face `expression` manipulation value (`Happy`, `Sad`, etc.)
* `page`: The name of the navigated page, could be `gallery`, `playground`, etc.
* `path`: The path of the navigated page, could be `/gallery`, `/gallery/{vhash}`, etc.

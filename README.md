# Instafeed2
Instafeed2 is an easy-to-use JavaScript library to add photos and more from your Instagram feed to your own website.

This is a remake of [Steven Schobert's original instafeed.js](https://github.com/stevenschobert/instafeed.js) in order to provide a full support of the latest Instagram API changes.

## Installation
Just download the script and include it into your HTML:
```html
<script src="path/to/instafeed2.js"></script>
```

### AMD/CommonJS
Instafeed2 also supports AMD and CommonJS:
```javascript
// AMD
require(["path/to/instafeed2"], function(Instafeed2) {

});

//CommonJS
var Instafeed2 = require("instafeed2");
```

### NPM
Instafeed2 is available on NPM:
```sh
$ npm install instafeed2
```

## Requirements
The only thing you will need is a valid access token from Instagram's API with a suitable access scope for your needs.
Learn more about [Login Permissions (Scopes)](https://www.instagram.com/developer/authorization/).

To get one, you can use the Instagram application [blackCICADA Access Token](https://www.blackcicada.com/apps/instagram/blackcicada_access_token/). It provides an access scope of `basic`.

## Basic Usage
```javascript
var instafeed2 = new Instafeed2({
    accessToken: "ACCESS_TOKEN"
});

instafeed2.run();
```

Instafeed2 will automatically search for an element with `id="instafeed2"` and fill it with thumbnails all of your last feeds. You can change this behavior using [Standard Options](#standard-options). Also check out the [Advanced Options](#advanced-options) for some advanced ways of customizing Instafeed2.

## Standard Options
* `accessToken` (string) -  __Required.__ A valid access token from Instagram's API.
* `get` (string) - Customize what Instafeed2 fetches:
    * `"user"` - __Default.__ Content from a specific user. __Requires access scope `basic` if `userId` is `"self"` or the own ID, otherwise `public_content`.__
    * `"tag"` -  Content with a specific tag. __Requires `tagName` and an access scope of `public_content`.__
    * `"location"` -  Content from a specific location. __Requires `locationId` and an access scope of `public_content`.__
* `userId` (string) -  __Default is `"self"`.__ Unique ID of a user to get content from. __Use with `get: "user"`.__
* `tagName` (string) -  Name of the tag to get content from. __Use with `get: "tag"`.__
* `locationId` (string) -  Unique ID of a location to get content from. __Use with `get: "location"`.__
* `limit` (number) -  __Default is `0` (maximum).__ Maximum number of content to add. The maximum is limited by Instagram.
* `sortBy` (string) - Sort the content in a set order:
    * `"none"` - __Default.__ As it comes from Instagram.
    * `"most-recent"` -  Newest to oldest.
    * `"least-recent"` -  Oldest to newest.
    * `"most-liked"` -  Highest number of likes to lowest.
    * `"least-liked"` -  Lowest number of likes to highest.
    * `"most-commented"` -  Highest number of comments to lowest.
    * `"least-commented"` -  Lowest number of comments to highest.
    * `"random"` -  Random order.
* `imageTemplate` (string) - __Default is `"<img src=\"{{image}}\">"`.__ Custom HTML template to use with images. See [Templating](#templating).
* `videoTemplate` (string) - __Default is `"<img src=\"{{image}}\">"`.__ Custom HTML template to use with videos. See [Templating](#templating).
* `carouselFrameTemplate` (string) - __Default is `"<img src=\"{{image}}\">"`.__ Custom HTML template to use with carousels. See [Templating](#templating).
* `carouselImageTemplate` (string) - __Default is `""`.__ Custom HTML template to use with images within carousels. See [Templating](#templating).
* `carouselVideoTemplate` (string) - __Default is `""`.__ Custom HTML template to use with videos within carousels. See [Templating](#templating).
* `imageResolution` (string) - Size of the images to get:
    * `"thumbnail"` - __Default.__ 150px x 150px.
    * `"low-resolution"` - 320px x 320px (varies in size).
    * `"standard-resolution"` - 640px x 640px (varies in size).
* `videoResolution` (string) - Size of the videos to get:
    * `"standard-resolution"` - __Default.__ 480px x 480px.
    * `"low-bandwidth"` - 480px x 480px (varies in size).
    * `"low-resolution"` - 480px x 480px (varies in size).
* `relativeScheme` (boolean) - __Default is `false`.__ Set to `true` to use protocol-relative URL schemes for the external content.
* `targetId` (string) - __Default is `"instafeed2"`.__ The ID of the DOM element where you want to add the content to.

## Advanced Options
* `mock` (boolean) -  __Default is `false`.__ Set to `true` to fetch data without inserting content into DOM. __Use with  `success`.__
* `filter` (function) - A function to filter the content. It will be given the current JSON object as an argument. To filter out content, the function has to return `false`, otherwise `true`.
* `onBefore` (function) -  A callback function called before content is added to the target element.
* `onAfter` (function) -  A callback function called after content has been added to the target element.
* `onSuccess` (function) -  A callback function called when everything is done without an error. It will be given the response from Instagram as JSON object as an argument.
* `onError` (function) - A callback function called when an error occurred. It will be given a string describing the error as an argument.

For example, how to get the user's content tagged with `awesome`:

```javascript
var instafeed2 = new Instafeed2({
    accessToken: "ACCESS_TOKEN",

    filter: function(data) {
        return data.tags && data.tags.find(function(tag) {
            return tag === "awesome";
        });
    },

    onSuccess: function() {
        alert("Instafeed2: Success!");
    },

    onError: function(message) {
        alert(message);
    }
});

instafeed2.run();
```

## Templating
To control the way Instafeed2 looks is to use the template option. You can write your own HTML markup and it will be used for everything that Instafeed2 fetches. For example, you can link every image to the original content on Instagram:

```javascript
var instafeed2 = new Instafeed2({
    accessToken: "ACCESS_TOKEN",
    get: "tag",
    tagName: "awesome",
    imageTemplate: "<a class=\"instagram\" href=\"{{link}}\"><img src=\"{{image}}\"></a>"
});

instafeed2.run();
```

The templating option provides several tags for you to use to control where variables are inserted into your HTML markup.

### Image Templating
Available tags for `imageTemplate` are:
* `{{id}}` - Unique ID of the image.
* `{{type}}` - Type of the content. __Can be `"image"`, `"video"`, or `"carousel"` (here `"image"`).__
* `{{userId}}` - Unique ID of the user.
* `{{username}}` - User's username on Instagram.
* `{{fullName}}` - Full name of the user.
* `{{profilePicture}}` - URL to the profile picture of the user (150px x 150px).
* `{{image}}` - URL to the image on Instagram.
* `{{width}}` - Width of the image in pixels.
* `{{height}}` - Height of the image in pixels.
* `{{orientation}}` - Orientation of the image. __Can be `"square"`, `"portrait"` or `"landscape"`.__
* `{{filter}}` - The name of the filter the image was edited with.
* `{{caption}}` - Caption text of the image. __Default is `""` if there is none.__
* `{{likes}}` - Number of likes the image has.
* `{{userHasLiked}}` - Defines whether the user has liked this image or not. __Can be `"true"` or `"false"`.__
* `{{comments}}` - Number of comments the image has.
* `{{tags}}` - An array containing the tags of the image as JSON string.
* `{{location}}` - Name of the location associated with the image. __Default is `""` if there is none.__
* `{{latitude}}` - The latitude of the location. __Default is `""` if there is no location associated.__
* `{{longitude}}` - The longitude of the location. __Default is `""` if there is no location associated.__
* `{{usersInPhoto}}` - An array containing the marked users and their positions in the image as JSON string.
* `{{createdTime}}` - Creation time of the image as Unix Timestamp.
* `{{link}}` - URL to the normal view of the image on Instagram.
* `{{model}}` - Full JSON object of the image content. If you want to get a property of the content that is not listed above, you can access it using dot-notation.

### Video Templating
For `videoTemplate` are the following tags available:
* `{{id}}` - Unique ID of the video.
* `{{type}}` - Type of the content. __Can be `"image"`, `"video"`, or `"carousel"` (here `"video"`).__
* `{{userId}}` - Unique ID of the user.
* `{{username}}` - User's username on Instagram.
* `{{fullName}}` - Full name of the user.
* `{{profilePicture}}` - URL to the profile picture of the user (150px x 150px).
* `{{previewImage}}` - URL to the preview image on Instagram.
* `{{previewWidth}}` - Width of the preview image in pixels.
* `{{previewHeight}}` - Height of the preview image in pixels.
* `{{previewOrientation}}` - Orientation of the preview image. __Can be `"square"`, `"portrait"` or `"landscape"`.__
* `{{image}}` - URL to the video on Instagram.
* `{{width}}` - Width of the video in pixels.
* `{{height}}` - Height of the video in pixels.
* `{{orientation}}` - Orientation of the video. __Can be `"square"`, `"portrait"` or `"landscape"`.__
* `{{filter}}` - Name of the filter the video was edited with.
* `{{caption}}` - Caption text of the video. __Default is `""` if there is none.__
* `{{likes}}` - Number of likes the video has.
* `{{userHasLiked}}` - Defines whether the user has liked this video or not. __Can be `"true"` or `"false"`.__
* `{{comments}}` - Number of comments the video has.
* `{{tags}}` - An array containing the tags of the video as JSON string.
* `{{location}}` - Name of the location associated with the video. __Default is `""` if there is none.__
* `{{latitude}}` - The latitude of the location. __Default is `""` if there is no location associated.__
* `{{longitude}}` - The longitude of the location. __Default is `""` if there is no location associated.__
* `{{createdTime}}` - Creation time of the video as Unix Timestamp.
* `{{link}}` - URL to the normal view of the video on Instagram.
* `{{model}}` - Full JSON object of the video content. If you want to get a property of the content that is not listed above, you can access it using dot-notation.

### Carousel Templating
Within carousels there are separated templates for images and videos.

For images use `carouselImageTemplate` and these tags:
* `{{type}}` - Type of the content. __Can be `"image"` or `"video"` (here `"image"`).__
* `{{image}}` - URL to the image on Instagram.
* `{{width}}` - Width of the image in pixels.
* `{{height}}` - Height of the image in pixels.
* `{{orientation}}` - Orientation of the image. __Can be `"square"`, `"portrait"` or `"landscape"`.__
* `{{usersInPhoto}}` - An array containing the marked users and their positions in the image as JSON string.

For videos use `carouselVideoTemplate` and the following tags:
* `{{type}}` - Type of the content. __Can be `"image"` or `"video"` (here `"video"`).__
* `{{video}}` - URL to the video on Instagram.
* `{{width}}` - Width of the video in pixels.
* `{{height}}` - Height of the video in pixels.
* `{{orientation}}` - Orientation of the video. __Can be `"square"`, `"portrait"` or `"landscape"`.__

To bundle these images and videos you can create a frame with `carouselFrameTemplate` and these tags:
* `{{id}}` - Unique ID of the carousel.
* `{{type}}` - Type of the content. __Can be `"image"`, `"video"`, or `"carousel"` (here `"carousel"`).__
* `{{userId}}` - Unique ID of the user.
* `{{username}}` - User's username on Instagram.
* `{{fullName}}` - Full name of the user.
* `{{profilePicture}}` - URL to the profile picture of the user (150px x 150px).
* `{{previewImage}}` - URL to the preview image on Instagram.
* `{{previewWidth}}` - Width of the preview image in pixels.
* `{{previewHeight}}` - Height of the preview image in pixels.
* `{{previewOrientation}}` - Orientation of the preview image. __Can be `"square"`, `"portrait"` or `"landscape"`.__
* `{{carouselMedia}}` - Every image and video will be parsed using the corresponding templating option and inserted at this tag.
* `{{filter}}` - Name of the filter the carousel was edited with.
* `{{caption}}` - Caption text of the carousel. __Default is `""` if there is none.__
* `{{likes}}` - Number of likes the carousel has.
* `{{userHasLiked}}` - Defines whether the user has liked this carousel or not. __Can be `"true"` or `"false"`.__
* `{{comments}}` - Number of comments the carousel has.
* `{{tags}}` - An array containing the tags of the carousel as JSON string.
* `{{location}}` - Name of the location associated with the carousel. __Default is `""` if there is none.__
* `{{latitude}}` - The latitude of the location. __Default is `""` if there is no location associated.__
* `{{longitude}}` - The longitude of the location. __Default is `""` if there is no location associated.__
* `{{createdTime}}` - Creation time of the carousel as Unix Timestamp.
* `{{link}}` - URL to the normal view of the carousel on Instagram.
* `{{model}}` - Full JSON object of the carousel content. If you want to get a property of the content that is not listed above, you can access it using dot-notation.

## Pagination
Instafeed2 has a `next` method you can call to load more content from Instagram. Under the hood, this uses the pagination data from the Instagram API. Additionally, there is a helper method called `hasNext` that you can use to check if pagination data is available.

Together these options can be used to create a *Load More* button:
```javascript
var loadMoreButtonElement = document.getElementById("loadMoreButton");

var instafeed2 = new Instafeed2({
    accessToken: "ACCESS_TOKEN",

    onSuccess: function() {
        if(this.hasNext()) loadMoreButtonElement.disabled = false;
    }
});

loadMoreButtonElement.addEventListener("click", function() {
    loadMoreButtonElement.disabled = true;
    instafeed2.next();
});

instafeed2.run();
```

The `hasNext` method only returns reliable data after every `onSuccess` callback. Until that it will return always `false`.

## Credits
Instafeed2 is maintained by [blackCICADA](https://github.com/blackCICADA). All open source code released by blackCICADA is licensed under the Apache License 2.0.

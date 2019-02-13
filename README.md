# pig-react

> Arrange images in a responsive, progressive-loading grid managed in JavaScript using CSS transforms.

[![NPM](https://img.shields.io/npm/v/pig-react.svg)](https://www.npmjs.com/package/pig-react) [![JavaScript Style Guide](https://img.shields.io/badge/code_style-standard-brightgreen.svg)](https://standardjs.com)


A re-implementation of @schlosser's [pig.js](https://github.com/schlosser/pig.js/) in React.

Instead of hosting images locally I've used Cloudinary - an image hosting provider, which adds the benefit of on-the-fly image resizing. So we don't need to worry about managing/generating multiple versions of an image at different resolutions. Like the original version of Pig.js we still need to provide an array of objects containing metadata about each image. I've created a node script `upload.js` to do this for us, see below section on `upload.js`.

In addition each image is now clickable and will expand to the center of the screen. It uses React Spring to handle the transition.


## upload.js
Using this file assumes you are using Cloudinary as your image hosting provider. You could take the concepts within it and write it for your own image hosting provider of choice.

Running the file will;
* Loop through a local folder of images and uploads them all to Cloudinary
* Generates metadata for each image, including url, aspectRatio, dominant colour (useful for preload styling), file birthtime (useful for sorting photos by date) and exif data.
* On completion of the upload, it generates a JSON file containing metadata about the collection of images. This JSON file can then be plugged into Pig.

To use upload.js
1. Create a Cloudinary account. 
1. Create a file named `.env` and save it in the same folder as `upload.js`. Paste in the following with your Cloudinary credentials filled out;
```
cloud_name="abc123"
api_key="1234567890"
api_secret="yourapisecret"
cloudinaryFolder="yourfolderincloudinary"
```

3. Set the `localImgFolder` and `outputJSONPath` variables accordingly
1. Install the cloudinary package with `npm i -D cloudinary` or `yarn add -D cloudinary`.
1. Run `node upload.js`

## Example usage of Pig

```
import Pig from './lib/Pig'
import imageData from './imageData.json' // the file generated by upload.js

class App extends Component {
  render() {
    return (
      <Pig
        imageData={imageData}
        gridGap={10}
        urlGenerator={(url, pxHeight) => {
          // Pig calls this function every time it needs to fetch an image,
          // providing its required image height in pixels.
          // This gives you flexibility to define what the url looks like
          // to suit the url scheme of your image hosting provider
          
          // http://res.cloudinary.com/cloudinaryusername/image/upload/h_{{HEIGHT}}/v1234567890/cloudinaryfolder/imagefilename.jpg
          return url.replace('{{HEIGHT}}', pxHeight)
        }}
      />
    )
  }
}

export default App
```


## Todo: 
- [x] Convert this package into a module
- [ ] Use resize observer
- [ ] Group by months
- [ ] Proptype checks

This React library was packaged with https://github.com/transitive-bullshit/create-react-library

## License

MIT © [nickmcmillan](https://github.com/nickmcmillan)
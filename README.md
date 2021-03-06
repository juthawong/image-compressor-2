# image-compressor

> A simple JavaScript image compressor. Uses the Browser's native [canvas.toBlob](https://developer.mozilla.org/en-US/docs/Web/API/HTMLCanvasElement/toBlob) API to do the compression work. General use this to precompress a client image file before upload it.

- [Website](https://xkeshi.github.io/image-compressor)

## Table of contents

- [Main](#main)
- [Getting started](#getting-started)
- [Options](#options)
- [Methods](#methods)
- [Browser support](#browser-support)
- [Versioning](#versioning)
- [License](#license)

## Main

```txt
dist/
├── image-compressor.js        (UMD)
├── image-compressor.min.js    (UMD, compressed)
├── image-compressor.common.js (CommonJS, default)
└── image-compressor.esm.js    (ES Module)
```

## Getting started

### Installation

- [Download the latest release](https://github.com/xkeshi/image-compressor/archive/master.zip).
- Clone the repository: `git clone https://github.com/xkeshi/image-compressor.git`.
- Install with [NPM](https://npmjs.com): `npm install xkeshi/image-compressor --force`.

### Usage

#### Syntax

```js
new ImageCompressor([file[, options]])
```

**file**

- Type: [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File) or [`Blob`](https://developer.mozilla.org/en-US/docs/Web/API/Blob)
- Optional

The target image file for compressing.

**options**

- Type: `Object`
- Optional

The options for compressing. Check out the available [options](#options).

#### Example

```html
<input type="file" id="file" accept="image/*">
```

```js
import axios from 'axios';
import ImageCompressor from 'image-compressor';

document.getElementById('file').addEventListener('change', (e) => {
  const file = e.target.files[0];

  if (!file) {
    return;
  }

  new ImageCompressor(file, {
    quality: .6,
    success(result) {
      const formData = new FormData();

      formData.append('file', result);

      // Send the compressed image file to server with XMLHttpRequest.
      axios.post('/path/to/upload', formData).then(() => {
        console.log('Upload success!');
      });
    },
    error(e) {
      console.log(e.message);
    },
  });
})
```

[⬆ back to top](#table-of-contents)

## Options

### maxWidth

- Type: `number`
- Default: `Infinity`

The max width of the output image. The value should be greater then `0`.

### maxHeight

- Type: `number`
- Default: `Infinity`

The max height of the output image. The value should be greater then `0`.

### minWidth

- Type: `number`
- Default: `0`

The min width of the output image. The value should be greater then `0` and should not be greater than the `maxWidth`.

### minHeight

- Type: `number`
- Default: `0`

The min height of the output image. The value should be greater then `0` and should not be greater than the `maxHeight`.

### width

- Type: `number`
- Default: `undefined`

The width of the output image. If not specified, the natural width of the original image will be used, or if the `height` option is set, the width will be computed automatically by the natural aspect ratio.

### height

- Type: `number`
- Default: `undefined`

The height of the output image. If not specified, the natural height of the original image will be used, or if the `width` option is set, the height will be computed automatically by the natural aspect ratio.

**Note:** In order to keep the same aspect ratio to the original image, if the `width` option is set, will use it compute the height automatically, which means the `height` option will be ignored.

### quality

- Type: `number`
- Default: `0.8`

The quality of the output image.
It must be a number between `0` and `1`. Be careful to use `1` as it may make the size of the output image become larger.
Check out [canvas.toBlob](https://developer.mozilla.org/en-US/docs/Web/API/HTMLCanvasElement/toBlob) for more detail.

**Note:** This option only available for `image/jpeg` and `image/webp` images.

**Examples** (in Chrome 59):

| Quality | Input size | Output size | Compression ratio | Description |
| --- | --- | --- | --- | --- |
| 0 | 2.12 MB | 229.56 KB | 89.43% | - |
| 0.2 | 2.12 MB | 422.82 KB | 80.53% | - |
| 0.4 | 2.12 MB | 578.25 KB | 73.37% | - |
| 0.6 | 2.12 MB | 747.85 KB | 65.56% | Recommend |
| 0.8 | 2.12 MB | 1.18 MB | 44.14% | Recommend |
| 1 | 2.12 MB | 2.12 MB | 0% | Not recommend |
| NaN | 2.12 MB | 2.05 MB | 3.19% | - |

### mimeType

- Type: `string`
- Default: `'auto'`

The mime type of the output image. By default, the original mime type of the source image file will be used.

### convertSize

- Type: `number`
- Default: `5000000` (5MB)

PNG files over this value will be converted to JPEGs. To disable this, just set the value to `Infinity`. See [#2](https://github.com/xkeshi/image-compressor/issues/2).

**Examples** (in Chrome 59):

| convertSize | Input size (type) | Output size (type) | Compression ratio |
| --- | --- | --- | --- |
| 5 MB | 1.87 MB (PNG) | 1.87 MB (PNG) | 0% |
| 5 MB | 5.66 MB (PNG) | 464.69 KB (JPEG) | 91.98% |
| 5 MB | 9.74 MB (PNG) | 872.13 KB (JPEG) | 91.26% |

### success(result)

- Type: `Function`
- Default: `null`
- Parameters:
  - `result`: The compressed image (a `Blob` object).

The success callback for the image compressing process.

### error(err)

- Type: `Function`
- Default: `null`
- Parameters:
  - `err`: The compression error (an `Error` object).

The error callback for the image compressing process.

[⬆ back to top](#table-of-contents)

## Methods

### compress(file[, options])

- `file`:
  - Type: `File` or `Blob`
  - The target image file for compressing.
- `options` (optional):
  - Type: `Object`
  - The options for compressing.
- (return value):
  - Type: `Promise`

Compress an image file.

```js
const imageCompressor = new ImageCompressor();

imageCompressor.compress(file, options)
  .then((result) => {
    // Handle the compressed image file.
  })
  .catch((err) => {
    // Handle the error
  })
```

[⬆ back to top](#table-of-contents)

## Browser support

- Chrome (latest)
- Firefox (latest)
- Safari (latest)
- Opera (latest)
- Edge (latest)
- Internet Explorer 10+ (requires `babel-polyfill` for `Promise` support)

## Versioning

Maintained under the [Semantic Versioning guidelines](http://semver.org/).

## License

[MIT](http://opensource.org/licenses/MIT) © [Xkeshi](http://xkeshi.com)

[⬆ back to top](#table-of-contents)

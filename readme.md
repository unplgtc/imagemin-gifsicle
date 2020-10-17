# imagemin-gifsicle [![Build Status](https://travis-ci.org/imagemin/imagemin-gifsicle.svg?branch=master)](https://travis-ci.org/imagemin/imagemin-gifsicle)

> Fork of the Imagemin plugin for [Gifsicle](https://www.lcdf.org/gifsicle/) which removes max buffer size and includes more Gifsicle commands. This fork is the same as the base, but included the code from two open PRs to the main repo which have been unmerged for quite some time: [Don't throw on inputs larger than 10 MB](https://github.com/imagemin/imagemin-gifsicle/pull/46) and [Include additional options from the gifsicle command line](https://github.com/imagemin/imagemin-gifsicle/pull/42).

> Once the aforementioned PRs are merged I will no longer be maintaining this fork, and may delete it entirely, so if you come across this I do not recommend adding it as a dependency.

## Install

```
$ npm install @unplgtc/imagemin-gifsicle
```

## Usage

```js
const imagemin = require('imagemin');
const imageminGifsicle = require('@unplgtc/imagemin-gifsicle');

(async () => {
	await imagemin(['images/*.gif'], {
		destination: 'build/images',
		plugins: [
			imageminGifsicle()
		]
	});

	console.log('Images optimized');
})();
```

## API

### imageminGifsicle(options?)(buffer)

Returns a `Promise<Buffer>` with the optimized image.

#### options

Type: `object`

##### interlaced

Type: `boolean`\
Default: `false`

Interlace gif for progressive rendering.

##### optimizationLevel

Type: `number`\
Default: `1`

Select an optimization level between `1` and `3`.

> The optimization level determines how much optimization is done; higher levels take longer, but may have better results.

1. Stores only the changed portion of each image.
2. Also uses transparency to shrink the file further.
3. Try several optimization methods (usually slower, sometimes better results)

##### colors

Type: `number`

Reduce the number of distinct colors in each output GIF to num or less. Num must be between 2 and 256.

##### lossy

Type: `number`\
Default: `20`

Alter image colors to shrink output file size at the cost of artifacts and noise. Lossiness determines how many artifacts are allowed; higher values can result in smaller file sizes, but cause more artifacts.

##### colorMethod

Type: `string`\
Default: `diversity`

Determine how a smaller colormap is chosen. `diversity`, the default, is xv(1)’s diversity algorithm, which uses a strict subset of the existing colors and generally produces good results. `blend-diversity` is a modification of this: some color values are blended from groups of existing colors. `median-cut` is the median cut algorithm described by Heckbert.

##### ditherMethod

Type: `string`\
Default: `floyd-steinberg`

Specify a dithering algorithm with the optional method argument. The default, `floyd-steinberg`, uses Floyd-Steinberg error diffusion. This usually looks best, but can cause animation artifacts, because dithering choices will vary from frame to frame. Gifsicle also supports ordered dithering algorithms that avoid animation artifacts. The `ro64` mode uses a large, random-looking pattern and generally produces good results. The `o3`, `o4`, and `o8` modes use smaller, more regular patterns. The `ordered` mode chooses a good ordered dithering algorithm. For special effects, try the halftone modes `halftone`, `squarehalftone`, and `diagonal`. Some modes take optional parameters using commas. The halftone modes take a cell size and a color limit: `halftone,10,3` creates 10-pixel wide halftone cells where each cell uses up to 3 colors.


##### resize

Type: `string`\
Format: `widthxheight`

Resize the output GIF to the given width and height. If width or height is an underscore `_`, that dimension is chosen so that the aspect ratio remains unchanged.

##### resizeMethod

Type: `string`\
Default: `mix`

Set the method used to resize images. The `sample` method runs very quickly, but when shrinking images, it produces noisy results. The `mix` method is somewhat slower, but produces better-looking results. The default method is currently `mix`.

Other methods include `sample`, `box`, `mix`, `catrom`, `mitchell`, `lanczos2`, `lanczos3`.

##### resizeColors

Type: `number`

Allow Gifsicle to add intermediate colors when resizing images. Normally, Gifsicle’s resize algorithms use input images’ color palettes without changes. When shrinking images with very few colors (e.g., pure black-and-white images), adding intermediate colors can improve the results.


#### buffer

Type: `Buffer`

Buffer to optimize.

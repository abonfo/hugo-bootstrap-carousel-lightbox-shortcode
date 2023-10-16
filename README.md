# Hugo bootstrap carousel lightbox shortcode
 
This is a hugo module for a **shortcode** template `carousel.html` that shows one or multiple bootstrap 5 carousels in a single page.

The images for each carousel are pulled from **single subdirectories** inside your page bundle. The carousels can have the lightbox2 functionality (option can be set per-carousel).

The caption for the images in a subdirectory can be fetched from the images **filename** or from the **EXIF metadata _.ImageDescription_**. In this last case, you will need to fill the correponding EXIF field in the jpegs (for images whitout default EXIF metadata). This option to fetch from filename or from EXIF is a per-subdirectory option, not a per-file option. 

Until future development, images should be **manually resized** to the desired format and EXIF metadata shuld be inserted in the images in a jpeg format, if you do not want to use the filename as the carousel caption. The carousel will work even if you do not resize the images, but the result will be a carousel that will expand and contract to fit the single image original size. This is needed because in my tests, the resize function in Hugo will remove the EXIF metadata.  

An example of the result can be viewd at the page: https://studio.andrebonfanti.it/intelligenza-artificiale-generazione-render-da-schizzi/

## Disclaimer

I provide this code at the best of my knowledge of Hugo module usage, but you have to know the basics of Hugo module commands and configurations. The code itself is a simple shortcode template without harmful code, but the module integration and the conversion of your site to a Hugo module is up to you. I provide some guidance to convert your site and to import the module, but check first the Hugo Docs. 

## Getting Started

This shortcode module has a standard hugo tree structure:

    .                               # root folder
    ├── ...
    ├── layouts                     # Layouts folder
    │   ├── shortcodes              # Shortcodes folder
    │       ├── carousel.html       # carousel code                
    └── ...

### Prerequisites

#### Hugo

The module is tested against `hugo v0.111.3+extended`. I do not guarantee previous hugo versions.

#### Bootstrap

You must have a Hugo theme based on Bootstrap, as the template uses the bootstrap theme code.

The code is tested against Boostrap v5.2. The code could work on previous Bootstrap version, but you should test it before. For example, the "ride" option do not have the "true" attribute in Bootstrap v5.0 according to the Bootstrap v.5.0 documentation.

#### Lightbox and jquery scripts

Until future development to merge requirements inside this module, you must fetch `jquery` and `lightbox` javascripts in your scripts template page, or in your preferred location. For example, if your theme has a scripts.html template, you could set this tags inside: 

```
<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/lightbox2/2.11.1/js/lightbox.min.js"></script>
```

Or you can download the minified version of the scripts and put them in your asset/js folder, and link them inside your code.

#### Go and Git install

_You can skip this step if you already have Go and Git installed._ 

Hugo modules functionality require the installation of Go and Git on your local machine (see https://gohugo.io/hugo-modules/use-modules/). Refer to the link for installation before continuing.

#### Initialize your site as a hugo module

_You can skip this step if your site is already a hugo module._

Next, your site should be initialized as a `hugo module`. In the root of your site local folder: 

```
hugo mod init {project}
``` 

Usually, `project` is the URL of your online site repository without `https://`. For example:

 ```
 hugo mod init github.com/GITHUB-USER/MY-SITE
 ``` 
 
 If you do not have your site pushed to a remote repository like Github, `project` can be a unique name. For example: 
 
```
hugo mod init MY-CUSTOM-PROJECT-NAME
``` 

This creates two new files, `go.mod` for the module definitions and `go.sum` which holds the checksums for module verification. The `go.mod` file in your site root will contain now something like this:
```
module PROJECT

go 1.19
```

Where `PROJECT` is the name you gave with `hugo mod init PROJECT` command.

### Installation

After you have initialized your site as a hugo module, to install this shortcode module, edit your `config.yaml` file to add the `module` import specifications (refer to https://gohugo.io/hugo-modules/configuration/ for more options). I am using the yaml format, but you are free to convert this in *toml* or *json* format. 

```
module:
  imports:
  - path: github.com/abonfo/hugo-bootstrap-carousel-lightbox-shortcode
  ```

Then, you need to pull the imports defined in the config file. In the root folder of your site run:

```
hugo mod get
```
This command will:
* run all the imports defined in your config file;
* download this module;
* add a `require` row in your `go.mod` file with the module URL.

This is the code for the **production** config file. If you need to test the module on development or staging environment, refer to https://gohugo.io/getting-started/configuration/#configuration-directory for setting different environments, and to https://gohugo.io/hugo-modules/configuration/#module-configuration-top-level to use the `replacements` option to edit the module locally without pushing the commits to your forked repo of the module.

### Update

To update this module to the last version:

```
hugo mod get -u github.com/abonfo/hugo-bootstrap-carousel-lightbox-shortcode
```

To update this module to a certain tag version, for example to v1.2.0:

```
hugo mod get -u github.com/abonfo/hugo-bootstrap-carousel-lightbox-shortcode@v1.2.0
```

For other oprions, refer to https://gohugo.io/commands/hugo_mod_get.



## Usage

To use the shortcode, read the following instructions.

### Images folders structure

In the page you want to use the shortcode, create the subfolder/s to store your images, in the format `slider-XX`. The format must be `slider-XX`, as the `slider-` part is hardcoded in the template. For example:

    .                   # page folder
    ├── ...
    ├── slider-01       # Image folder
    ├── slider-02       # Image folder
    ├── ...                         
    ├── slider-NN       # Image folder
    ├── ...
    ├── index.md        # Content file
    └── ...

### Images size

Put your images in the folders. Images should be resized before, as your needs for each carousel dimensions. 

### Images carousel caption

If you want to use the images filename as the carousel caption, give the images a useful filename and set the `FILENAME` shortcode attribute (see below for the shortcode usage).

 If you want to use the EXIF metadata to store the image caption, you must populate the `ImageDescription` metadata tag, and set the `EXIF` shortcode attribute. 

### Shortcode parameters

Insert this shortcode in your page content where you want the carousel to appear. Each slider folder must have its own shortcode.

```
{{< carousel 
    slider_num="XX" 
    ride=carousel 
    interval=7000
    wrap=true 
    fade=true
    controls=true
    indicators=true
    dark=false
    caption_source=EXIF
    lightbox=true
>}}
```
Where the parameters are:
 * **slider_num** (REQUIRED): the number of the folder from which the shortcode should fetch the images. Keep the value in the quotes.  
 * **ride** (REQUIRED): use "carousel" to enable autoplay on load; use "false" to disable autoplay; use "true" to autoplay the carousel after the user manually cycles the first item. 
 * **interval** (REQUIRED): is the delay time between automatically cycling to the next item, in milliseconds. Insert your desired time in ms (ex. 5000). Can be "false" to disable, but must be a number if **ride** is set to "carousel" or "true".
 * **wrap** (REQUIRED): "true" or "false", to enable or disable the carousel cycling or have an hard stop at the end.
 * **fade** (OPTIONAL): "true" or "false", to enable or disable the fading of the images. You can also omit the whole parameter, and in this case the carousel will default to standard transition.
 * **controls** (OPTIONAL): "true" or "false", to enable or disable the left and right arrows. If the parameter is omitted, no arrows will be shown.
 * **indicators** (OPTIONAL): "true" or "false", to enable or disable the bottom indicators. If the parameter is omitted, no indicators will be shown.
 * **dark** (OPTIONAL): "true" or "false", to enable or disable dark controls, indicators, and captions. If the parameter is omitted, white theme colors will be used. You can override this behaviour by setting your colors in your custom css file.  
* **caption_source** (OPTIONAL): "EXIF" to use the image description text inside the image EXIF metadata (it fetches the _.ImageDescription_ tag); "FILENAME" to use the file name without the extension. If the parameter is omitted, no caption will be shown and no css class for the caption will be given.
* **lightbox** (OPTIONAL): "true" to apply lightbox functionality to a single canvas; "false" to not apply lightbox functionality to canvas. If the parameter is omitted, no lightbox functionality will be used (same as "false"). 

### Css usage

This module is built using the same classes from the bootstrap carousel component (refer to https://getbootstrap.com/docs/5.2/components/carousel/).

The carousel caption is wrapped with the standard bootstrap `carousel-caption` class. Inside this class, the module implements a custom `carousel-description` class that wraps the text caption. These two classes are available only if the **caption_source** parameter is given.


## Roadmap

- [ ] Add touch swiping bootstrap carousel option as shortcode parameter.
- [x] Add wrap bootstrap carousel option as shortcode parameter.
- [x] Add dark bootstrap carousel option as shortcode parameter.
- [ ] Add resize option parameter to dynamically set the image size (this can break EXIF metadata), or use other methods for scaling the images.
- [x] Add shortcode parameter to fetch a specific EXIF field, or fallback to image filename ~~for non-jpeg images, or other source for captions~~.  
- [ ] Add EXIF caption to lightbox pulled from jpeg metadata.
- [x] Add parameters and conditional functions to show or hide the `controls` in the carousel. 
- [x] Add parameters and conditional functions to show or hide the `indicators` in the carousel.
- [x] Add option to disable lightbox functionality and use only the carousel functions.
- [ ] Remove hardcoded "slider-" prefix from image folders and let user define custom folder names.
 
## Credits

This project is based on hugo documentation, tutorials and blogs. Thanks to:

* [Hugo Docs](https://github.com/gohugoio/hugoDocs/)
* [Future Web Design](https://futurewebdesign.au/)
* [The New Synamic - Hugo Module article](https://www.thenewdynamic.com/article/hugo-modules-everything-from-imports-to-create/)


## License

Distributed under the GNU General Public License v3.0. See `LICENSE.txt` for more information.

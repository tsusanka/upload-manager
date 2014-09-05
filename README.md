Upload Manager [![Build Status](https://travis-ci.org/ondrs/upload-manager.svg?branch=master)](https://travis-ci.org/ondrs/upload-manager)
==============

Upload manager for Nette framework

Instalation
-----

composer.json

    "ondrs/upload-manager": "dev-master"

Configuration
-----

Register the extension:

    extensions:
        uploadManager: ondrs\UploadManager\DI\Extension

Minimal configuration:

    uploadManager:
        relativePath: '/uploads'

Full configuration:

    uploadManager:
        basePath: %wwwDir%
        relativePath: '/uploads'
        fileManager:
            blackList: {php}
        imageManager:
            maxSize: 1280
            dimensions:
                800:
                    - {800, NULL}
                    - shrink_only
                500:
                    - {500, NULL}
                    - shrink_only
                250:
                    - {250, NULL}
                    - shrink_only
                thumb:
                    - {100, NULL}
                    - shrink_only

In most cases you want to choose the `wwwDir` as your `basePath` (and it is chosen by default) to make your files publicly accessible.
`relativePath` is relative to the `basePath`, so complete path where your files will be uploaded looks like this

    {basePath}/{relativePath}[/{dir}]

`dir` is an optional parameter and you can set it during the runtime of your script in the `listen()` or in the `upload()` method.

**fileManager:**
- blacklist
  - array of files extensions which are blacklisted, php is by default

**imageManager**
- maxSize
  - maximum size of an image, if its bigger, it will be automatically resized to this size
  - can be number X coord of an imahe
  - or array [X, Y]

- dimensions
  - array of dimensions to which an image will be resized
  - format is
    ```
    PREFIX:
        - {X_SIZE, Y_SIZE}
        - RESIZE_OPTION
    ```

  - `PREFIX` can be whatever you want, it will be added to a resized file: `PREFIX_file.jpg`
  - `Y_SIZE` is optional as well as `RESIZE_OPTION`
  - `RESIZE_OPTION` is set to `Image::SHRINK_ONLY` by default

For example we will set the UploadManager according to the Full configuration which is written above.

    $this->upload->listen('super/dir')

Uploading an image file `foo.jpg` with size (1680 x 1050) will result in creation of 5 files: `foo.jpg, 800_foo.jpg, 500_.jpg, 250_foo.jpg, thumb_foo.jpg`
which will be saved in the `%wwwDir%/uploads/super/dir`
All files are resized proportionally according to their X dimension and saved with a corresponding prefix.
File foo.jpg is considered to be an original but it's resized to 1280px.


Usage
-----

Inject `ondrs\UploadManager\Upload` into your presenter or wherever you want

    /** @var \ondrs\UploadManager\Upload @inject
    private $upload;

And listen for an upload.

    public function renderUpload()
    {
        $this->upload->listen('path/to/dir');
    }

If you want to upload just a single file, for example with a form, call directly the upload method

    public function processForm($form)
    {
        /** @var Nette\Http\FileUpload */
        $fileUpload = $form->values->file;

        $this->upload->upload($fileUpload, 'path/to/dir');
    }


Callbacks
-----

- onQueueBegin
- onQueueComplete
- onFileBegin
- onFileComplete

TODO: callbacks

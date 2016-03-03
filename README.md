![](./docs/logo_white_framed.png "Slicebox") Slicebox
=====================================================

This is a project gathering integration code for using Slicebox from Matlab, and other Slicebox-related Matlab code.

Introduction
------------

The Matlab integration uses the REST API to connect to an instance of slicebox. Several scripts and are provided which can be used to retrieve image information and dicom files. There is also a simple GUI which can list patients and series, show some very basic image information and can be used to retrieve images. Note that the GUI is created using Matlab for OSX, which limits its usefulness on other OS due to differences in font size etc.

The Matlab files are not included in the universal zip package. To use these files you must clone the repository where the files can be found in [./src/main/matlab/](/src/main/matlab/). Matlab 2015a or later is required to use these scripts.

For more detailed descriptions of file usage and usage examples, see [/matlab/README](src/main/matlab/README.txt), or the comments in the function files.

### Getting started

* Clone the repository and find the files in [./src/main/matlab/slicebox-integration](src/main/matlab/slicebox-integration).
* Edit the [./src/main/matlab/slicebox-integration/sbxsettings.conf](sbxsettings.conf). The file only contains two options, an url to an instance of slicebox (defaults to localhost:5000) and the path to a cache directory where dicom files and some offline data will be saved to allow offline usage of the GUI.
* Start `SBXGui_osx` in Matlab 2015a or later. Normal usage is `SBXGui_osx(username, password)`, but other options can be entered as well, see the readme.

File descriptions
-----------------

For more in depth usage documatation, read the in-file comments.

* `makesbxdata` - use this function to create an container for all the settings required to connect to a slicebox instance. The function will read some settings from the file `sbxsettings.conf` if it exists in the active matlab directory.
* `sbxgetflatseries` -- use this function to retrieve a flattened image of the patient database on the slicebox server. This can be used to manually browse through patients, studies or series, but is mainly used to populate the tables in the gui. Every time the function is used it will save the flattened image to the cache folder. If the server is offline or otherwise unreachable, the function will try to use the cached version instead.
* `sbxgetimageinfo` -- use this function to retrieve the information required to download the images in a series. The image information is saved to the cache directory and if the slicebox server is offline or otherwise unrechable the function will try to use the cached version.
* `sbxreadimages` -- this function will download the specified images and dicom headers from the slicebox server. The images will be saved in the cache directory, if not already present. If present, the images will be read from the cache instead.
* `sbxreadseries` -- will download the images and their dicom headers of the specified series. The images will be saved in the cache directory, if not already present. If present, the images will be read from the cache instead.
* `SBXGui_osx` -- provides a simple gui to select and download images from a slicebox server. Start the gui, providing a username and password, select a patient in the upper table and then select a series in the bottom table. Press Load to download the images. The GUI will terminate when all the images have been downloaded. The images will be saved in the cache directory, if not already present. If present, the images will be read from the cache instead. This GUI works best with OSX, but can be used with other OSes as well. You will find that when used with a different os, the font size will be slightly too large.

Example Usage
-------------

There are three ways of retrieving images. In all three cases, a `sbxdata` object must first be created, like so:

```
sbxdata = makesbxdata(username, password);
```

provided a `sbxsettings.conf` file exists. Otherwise the options `cachepath` and `url` must also be provided. Don't forget to use specify the port in the url!

If the seriesid is known (you can find this by browsing the slicebox online interface, or by looking through the output of `sbxgetflatseries`), use `sbxreadseries`

```
[I, dcminfo] = sbxreadseries(sbxdata, seriesid);
```

If you only want certain images from a series, you can first read the imageinfo, choose the images you want and then use `sbxreadimages` to load them.

```
imageinfo = sbxgetimageinfo(sbxdata, seriesid);
[I, dcminfo] = sbxreadimages(sbxdata, imageinfo(2:6));
```

To start the gui just write

```
[I, dcminfo] = SBXGui(username, password);
```

Again, if the `sbxsettings.conf` doesn't exist, you must provide the `cachcepath` and `url` options.

Slicebox API
------------

A full specification of the Slicebox API is available at [slicebox.github.io/slicebox](http://slicebox.github.io/slicebox).

License
-------

Slicebox is released under the [Apache License, version 2.0](./LICENSE).

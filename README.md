# Sound Map Visualizer

Simple version of Freesound Explorer that can be used to visualize artibtraty maps of sounds. 

Checkout and double click `index.html` to open the interface. Use the input box to set query terms to search in Freesound or type the URL to a JSON file with arbitrary data to be loaded.


#### How to provide data via JSON file

A `example_data.json` file is provided that you can use as a template and to test the JSON load functionality. To display the contents of that file in the map, just type `example_data.json` in the input box and press "Go!".

To provide other data you need to create a JSON file that has the same structure as the one shown in `example_data.json`. This is basically a dictionary with one single entry `results` which contains a list of sounds to be displayed. Each sound must contain the following attributes:

 * `id`: a unique ID for the sound.
 * `url`: URL to a web page where the sound is displayed (can be fake as well).
 * `name`: name/title given to the sound.
 * `username`: author of the sound.
 * `audio`: URL to an audio file that can be used to play this sound. Note that the example data file has URLs that point to Freesound, but this can also be the URL to a local file. For working with local files the optimal solution is to add an `audio_files` folder in this same repository and put all the sounds inside. Then in the `audio` field of each sound in your `results` list you set the relative path to the audio file which will be something like `audio_files/filename.mp3`.
 * `image`: URL to an image to be displayed when the sound is selected. Can be left blank and no image will be shown. Can be path to a local file (works same as in `audio` field).
 * `analysis`: dictionary with the feature values that will be used to compute the map. The features that should be here depend on the different map layouts that you want to be supported (see below).

#### Changing available map layouts

To allow different map layouts you must edit the HTML `select` element in `index.html` (starting at line 15 aprox). You can add new options there so that features in axis can be changed.

To add new layout options add new lines with the form:
```
<option value="descriptors_to_be_used">Option display name</option>
```

The `descriptors_to_be_used` part should start with either:

 * `xy&`: to add an option with a map in which one feature is be assigned in each dimension.
 * `tsne&`: to add an option in which a self-organizing map will be computed from a given multi-dimensional feature.

If using the `xy&` map type, the two features to be used in each of the axis must be indicated after `xy&` and separated by "&". For example, use `xy&sfx.duration&lowlevel.pitch.mean`
for setting duration in x axis (horizontal) and pitch mean in y axis (vertical).

In `tsne&` mode only 1 feature needs to be indicated. It is expected to be a multi-dimensional feature so
that tsne can compute a dimensionality reduction. For example, use `tsne&lowlevel.mfcc.mean` to get a map arranged by similarity according to MFCCs.

For the layouts to work, descriptors data need to be present in the Freesound response or in the loaded JSON file. The different descriptors available for queries to Freesound can be seen here: `https://freesound.org/apiv2/descriptors/`. When working with a JSON file, any descriptors will work as long as these are included in the `analysis` dict for each sound. Descriptors can be added in a nested structure, and their full name must include all the levels of the structure separated by periods. For example, given the following analysis structure:

```
"analysis": {
    "lowlevel": {
        "average_loudness": 0.9672642155158363,
        "pitch": {
            "mean": 571.4835995224398
        },
        "mfcc": {
            "mean": [
                -825.6259832507296,
                131.78120127431026,
                -31.75835864543137,
                26.313526350014126,
                -38.06251265399024,
                -19.53786147045546,
                -12.369042117865106,
                10.53405565043513,
                28.16895815943852,
                55.91404974526349,
                53.34956028357271,
                34.719174782856854,
                5.941316522273627
            ]
        }
    },
    "sfx": {
        "duration": 0.3396582096750846,
        "logattacktime": {
            "mean": -1.2876480550527212
        },
        "tristimulus": {
            "mean": [
                0.6642523322420417,
                0.3272743438766559,
                0.008473205246176873
            ]
        }
    }
}
```
the full name to be used for MFCCs should be `lowlevel.mfcc.mean`, while the name for duration `sfx.duration`.


#### Notes on running this in different browsers

Loading local JSON files might be problematic in some browsers due to cross origin requests restrictions. After some quick tests we've observed that in Firefox the JSON load feature works fine, but that for it to work in Chrome and Safari you'll need to run a trick and create a simple local HTTP server that serves the `index.html` file instead of opening it directly. To do that open a temrinal to the repsitory folder and simply run:
```
python -m SimpleHTTPServer 8000
```
Then point your browser at `http://localhost:8000` and all will work just fine.

# Sources
Sources can be included to display additional media related to events. Sources first appear in event cards below the summary description for each event after the user clicks on the down arrow. If the user then selects the source in the expanded card, an overlay appears displaying the source content media. One event can have multiple sources, and one source can be used for multiple events. 

## Creating Sources
Creating a `source` requires specifying a number of values in the ['Sources' Google Sheet](https://docs.google.com/spreadsheets/d/1UC7DkCFeUXHfpUxUGruExwFbP4pqVBdJLOKfo6wDDGk/edit#gid=1551269392):
- `id` labels should be unique and are used in the ['Events sheet'](https://docs.google.com/spreadsheets/d/1UC7DkCFeUXHfpUxUGruExwFbP4pqVBdJLOKfo6wDDGk/edit#gid=0) to connect specific sources to events. `id` can be non-descriptive and should not appear in the source card or overlay.
- `title` is the name of the source. This will appear next to the source thumbnails and icons when the card expands, and again above the media content in the source overlay.
- `thumbnail` is used to specify a thumbnail image for the source to be displayed in the expanded card. Thumbnails must be images in either `.jpg` or `.png` format. If no thumbnail is provided, `timemap` will provide a default icon depending on the source's `type`. Default icons exist for `type` values of `Eyewitness Tesimony`, `Government Data`, `Satellite Imagery`, `Second-Hand Testimony`, `Video`, `Photo`, and `Photobook`. If one of these valid types is not specified a '?' will be displayed as the source icon.
 - `description` describes the source, however is not displayed in the current version of `timemap`. This field will be useful for maintaners to better understand what the sources are when editing the ['Sources' Google Sheet](https://docs.google.com/spreadsheets/d/1UC7DkCFeUXHfpUxUGruExwFbP4pqVBdJLOKfo6wDDGk/edit#gid=1551269392).
 - `path` specifies the path to the content files which will displayed in an overlay. The value provided for `path` will be appended to the end of a string in the ['EXPORT_SOURCES' sheet'](https://docs.google.com/spreadsheets/d/1UC7DkCFeUXHfpUxUGruExwFbP4pqVBdJLOKfo6wDDGk/edit#gid=1584139420) which points to where those files are being served (`https://localhost:8000`in the example). When deploying `datasheet-server` and `timemap` in a different environment you will need to change this base URL in the formula for the `path*` columns to where source content files are being served.
> The example ['Sources' Google Sheet](https://docs.google.com/spreadsheets/d/1UC7DkCFeUXHfpUxUGruExwFbP4pqVBdJLOKfo6wDDGk/edit#gid=1551269392) only provides three paths per source but more can be added by creating additional sequentially numbered `path*` columns in your Google Sheet (*make sure to duplicate these columns in the 'EXPORT_SOURCES' sheet*).
- `type` is also not displayed in the current version of `timemap` but will determine the default source icon if you do not provide a `thumbnail`. The default types with associated thumbnails are specified in [sources.js](https://github.com/forensic-architecture/timemap/blob/develop/src/components/presentational/Card/Source.js) and are listed under `thumbnail` above.

The example ['Sources' Google Sheet](https://docs.google.com/spreadsheets/d/1UC7DkCFeUXHfpUxUGruExwFbP4pqVBdJLOKfo6wDDGk/edit#gid=1551269392) only includes one source per event, but more can be created by creating additional sequentially numbered `source*` columns *(remember to duplicate these columns in the ‘EXPORT_SOURCES’ sheet)*.

## Source File Types
One `source` can contain a number of different media files which can also be of different file types. For example, you can provide the `path` to a number of different images to create a `Photobook`. You could also have a video, a number of still images from it, a text transcript of certain passages of dialogue, and another text crediting the creators of the video collected together as one source.

The following source file types are currently supported:

Type| Supported Formats
----|------------------
Text|.md, .pdf
Video|.mp4
Image|.png, .jpg

---
subtitle: Form Widget
shortname: Media Finder
---
# Media Finder Field

`mediafinder` - renders a field for selecting an item from the media manager library. Expanding the field displays the media manager to locate a file. The resulting selection is a string as the relative path to the file.

```yaml
whitepaper_file:
    label: Whitepaper PDF
    type: mediafinder
```

The following [field properties](../form-fields.md) are supported and commonly used.

Property | Description
------------- | -------------
**label** | a name when displaying the form field to the user.
**default** | specifies a default string value, optional.
**comment** | places a descriptive comment below the field.
**mode** | the expected file type, either `file`, `folder` or `image`. Default: `file`.
**imageWidth** | if using image type, the preview image will be displayed to this width, optional.
**imageHeight** | if using image type, the preview image will be displayed to this height, optional.
**maxItems** | maximum number of items that can be selected.
**thumbOptions** | additional [resize options](../../extend/services/resizer.md) for generating the thumbnail, or pass `false` to disable thumb generation.

Set the `mode` to **image** to display a preview of the selected image.

```yaml
background_image:
    label: Background Image
    type: mediafinder
    mode: image
```

You may set the `mode` to **folder** to only allow selecting a media folder path.

```yaml
media_folder:
    label: Media Folder
    type: mediafinder
    mode: folder
```

::: tip
Unlike the [File Upload form widget](./widget-fileupload.md), the Media Finder form widget stores its data as a string representing the path to the media files selected within the Media Library. It should associate to a normal attribute on your model.
:::

## Selecting Multiple Items

The media finder will attempt to guess if multiple items can be selected based on the [jsonable attribute](../../extend/system/models.md) in the model, which is a requirement for storing multiple items.

You may limit the number of items that can be selected using the `maxItems` property.

```yaml
media_gallery:
    label: Image
    type: mediafinder
    mode: image
    maxItems: 10
```

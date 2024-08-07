---
subtitle: Content Field
shortname: Nested Items
---
# Nested Items Field

`nesteditems` - creates nested records belonging exclusively to the current record.

```yaml
items:
    label: Menu Items
    type: nesteditems
    span: adaptive
    form:
        fields:
            title:
                label: Title
                type: text
```

<VideoBlockLink src="https://www.youtube.com/watch?v=vhs9U3_BHqg" title="Nested Items Tutorial" description="This video demonstrates how to implement the Nested Items content field using step by step instructions." prompt="Watch the tutorial" />

The following properties are supported.

Property | Description
------------- | -------------
**label** | a name when displaying the form field to the user.
**default** | specifies a default array value, optional.
**comment** | places a descriptive comment below the field.
**form** | inline form field definitions.
**maxDepth** | displays an interface for reordering records, specifying the maximum depth. Set to `0` for unlimited depth.
**customMessages** | customize the messages used in the user interface.

Like any other form, nested items support the use of tabs by placing the fields under the `tabs` or `secondaryTabs` properties of the `form` definition.

```yaml
tabbed_content:
    type: nesteditems
    form:
        tabs:
            fields:
                # ...
```

The `customMessages` property is used to modify the various messages used on the field definition. The available messages are shared with the [Relation Controller behavior](../../extend/forms/relation-controller.md) messages.

```yaml
author:
    type: nesteditems
    customMessages:
        buttonCreate: New Author
        titleUpdateForm: Update Author
        titleCreateForm: Create Author
```

#### See Also

::: also
* [Entries Content Field](./field-entries.md)
* [Repeater Form Field](../form/widget-repeater.md)
:::

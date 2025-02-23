# sanity-plugin-hotspot-array

A configurable Custom Input for Arrays that will add and update items by clicking on an Image

## Installation

```
sanity install hotspot-array
```

![2021-12-22 15 07 58](https://user-images.githubusercontent.com/9684022/147113578-d49d747a-4904-45b4-b4e0-409b33832226.gif)

## Setup

Import the `HotspotArray` component from this package, into your schema. And insert it as the `inputComponent` of an `array` field.

```js
import HotspotArray from 'sanity-plugin-hotspot-array'

export default {
  name: `product`,
  title: `Product`,
  type: `document`,
  fields: [
    {
      name: `hotspots`,
      type: `array`,
      inputComponent: HotspotArray,
      of: [
        // see `Spot object` setup below
      ],
      options: {
        // see `Image and description path` setup below
        hotspotImagePath: `featureImage`,
        hotspotDescriptionPath: `details`,
        // see `Custom tooltip` setup below
        hotspotTooltip: undefined,
      },
    },
    // ...all your other fields
  ],
}
```

The plugin makes a number of assumptions to add and update data in the array. Including:

- The `array` field is an array of objects
- The object contains two number fields named `x` and `y`
- You'll want to save those values as % from the top left of the image
- The same document contains the image you want to add hotspots to

### Image and description paths

The custom input has the current values of all fields in the document, and so can "pick" the image out of the document by its path.

For example, if you want to add hotspots to an image, and that image is uploaded to the field `featuredImage`, your fields `options` would look like:

```js
options: {
  hotspotImagePath: `featureImage`
}
```

The custom input can also pre-fill a string or text field with a description of the position of the spot to make them easier to identify. Add a path **relative to the spot object** for this field.

```js
options: {
  hotspotDescriptionPath: `details`
}
```

### Spot object

Here's an example object schema complete with initial values, validation, fieldsets and a styled preview.

```js
{
  name: 'spot',
  type: 'object',
  fieldsets: [{name: 'position', options: {columns: 2}}],
  fields: [
    {name: 'details', type: 'text', rows: 2},
    {
      name: 'x',
      type: 'number',
      readOnly: true,
      fieldset: 'position',
      initialValue: 50,
      validation: (Rule) => Rule.required().min(0).max(100),
    },
    {
      name: 'y',
      type: 'number',
      readOnly: true,
      fieldset: 'position',
      initialValue: 50,
      validation: (Rule) => Rule.required().min(0).max(100),
    },
  ],
  preview: {
    select: {
      title: 'details',
      x: 'x',
      y: 'y',
    },
    prepare({title, x, y}) {
      return {
        title,
        subtitle: x && y ? `${x}% x ${y}%` : `No position set`,
      }
    },
  },
}
```

## Custom tooltip

You can customise the Tooltip to display any Component, it will accept a single prop `spot` which contains the values of the object.

In this example our `spot` object has a `reference` field to the `product` schema type, and will show a Document Preview.

```js
// Setup a custom tooltip component
import Preview from 'part:@sanity/base/preview'
import schema from 'part:@sanity/base/schema'
import {Box} from '@sanity/ui'

export default function ProductPreview({spot}) {
  return (
    <Box padding={2} style={{minWidth: 200}}>
      {spot?.product?._ref ? (
        <Preview value={{_id: spot.product._ref}} type={schema.get(`product`)} />
      ) : (
        `No Reference Selected`
      )}
    </Box>
  )
}
```

Then back in your schema definition

```js
import HotspotArray from 'sanity-plugin-hotspot-array'
import ProductPreview from '../../components/ProductPreview'

options: {
 hotspotImagePath: `hotspotImage`,
 hotspotTooltip: ProductPreview,
},
```

## License

MIT © Simeon Griggs
See LICENSE

---
layout: code
type: module
title:  "xform-to-json"
date:   2016-03-17 20:43:00 -0700
categories: ['']
featured: false
description: "Converts XForm submissions from ODK Collect to json or geojson."
---
[![Build Status](https://travis-ci.org/digidem/xform-to-json.svg)](https://travis-ci.org/digidem/xform-to-json)


Converts XForm submissions from [ODK Collect](https://opendatakit.org/use/collect/) to json or geojson.

## Usage

```javascript
var xform2json = require('xform-to-json');

xform2json(xmlString, function(err, json) {
    console.log(json);
})
```

## `xform2json(xmlString, [options], callback)`

ODK sends an xml file with a single root node, which is discarded.

By default the following metadata is created in the json output:

```javascript
form.meta = {
  instanceId: form.meta.instanceID || 'uuid:' + uuid.v1(),
  instanceName: form.meta.instanceName || formKey,
  formId: form.$.id,
  version: form.$.version
};
```

Where:

`form.meta` is the original meta data from the ODK xml submission
`formKey` is the name of the original xml root node
`form.$.id` and `form.$.version` are the two attributes on the root node from ODK xml.

### `options.geojson`

When set to true will output geojson, using a geopoint field from the xml for the coordinates if present. If more than one geopoint field is present, the field is chosen arbitrarily unless `options.locationField` is set (see below). If no geopoint / location field is found then geojson geometry is `null`.

### `options.locationField`

The fieldname to use for the geojson coordinates.

### `options.meta`

Pass a hash of any metadata values to add to the form, e.g. `meta.submissionTime`.

## Contributors

* [Beau Gunderson](https://github.com/beaugunderson)

* [Gregor MacLennan](https://github.com/gmaclennan)




======================

See it on [GitHub](https://github.com/digidem/xform-to-json)
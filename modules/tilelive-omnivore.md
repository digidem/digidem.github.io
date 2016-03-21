---
layout: module
title:  "tilelive-omnivore"
date:   2016-03-17 20:43:00 -0700
categories: ['']
featured: false
description: "Implements the tilelive api for a variety of raw data sources."
---


Implements the tilelive api for a variety of raw data sources

[![Build Status](https://travis-ci.org/mapbox/tilelive-omnivore.svg?branch=master)](https://travis-ci.org/mapbox/tilelive-omnivore)
[![Coverage Status](https://coveralls.io/repos/mapbox/tilelive-omnivore/badge.svg?branch=master&service=github)](https://coveralls.io/github/mapbox/tilelive-omnivore?branch=master)

## Install

```
npm install -g tilelive-omnivore
```

## Example

```javascript
var Omnivore = require('tilelive-omnivore');
var filepath = '/absolute/path/to/geographic/data';
var uri = 'omnivore://' + filepath;

new Omnivore(uri, function(err, source) {
  source.getInfo(function(err, info) {
    console.log(info);
  });
});
```

## Works with

any file supported by [mapnik-omnivore](https://github.com/mapbox/mapnik-omnivore)



======================

See it on [GitHub](https://github.com/digidem/tilelive-omnivore).

Return to [Modules](/modules).
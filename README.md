# Geohash-poly

`npm install geohash-poly-coverage`

Transform a GeoJSON (Multi)Polygon to a list of geohashes that cover it.

Method used is pretty brute-force, but still relatively quick compared to alternative implementations if hash precision is not too granular. Creates an envelope around poly, and iterates over rows and columns, including relevant hashes with respect to `hashMode`.


## Streaming
Hashes can be streamed. Each _read will generate a row of hashes into buffer, as some form of throttling. This allows massive polygons with high precision hashes to avoid memory constraint issues. If your polys have the potential to hit memory issues, use this method.

If you specify rowMode as true, such as `.stream({..., rowMode: true, ...})`, each chunk in the stream will be an array using streams2 objectMode.

```javascript

var through2 = require('through2');

var polygon = [[[-122.350051, 47.702893 ], [-122.344774, 47.702877 ], [-122.344777, 47.70324 ], [-122.341982, 47.703234 ], [-122.341959, 47.701421 ], [-122.339749, 47.701416 ], [-122.339704, 47.69776 ], [-122.341913, 47.697797 ], [-122.341905, 47.697071 ], [-122.344576, 47.697084 ], [-122.344609, 47.697807 ], [-122.349999, 47.697822 ], [-122.350051, 47.702893 ]]];

var stream = geohashpoly.stream({
	coords: polygon,
	precision: 7,
	rowMode: true
});

stream
  .on('end', function () {
    console.log("It's all over.");
  })
  .pipe(through2(function (chunk, enc, callback) {
    console.log(chunk.toString());
    callback();
  }));

```

Results in the hashes spit out line by line to the console.


## Standard
If you just want your hashes out in an array, use this.
```javascript

var polygon = [[[-122.350051, 47.702893 ], [-122.344774, 47.702877 ], [-122.344777, 47.70324 ], [-122.341982, 47.703234 ], [-122.341959, 47.701421 ], [-122.339749, 47.701416 ], [-122.339704, 47.69776 ], [-122.341913, 47.697797 ], [-122.341905, 47.697071 ], [-122.344576, 47.697084 ], [-122.344609, 47.697807 ], [-122.349999, 47.697822 ], [-122.350051, 47.702893 ]]];

geohashpoly({coords: polygon, precision: 7}, function (err, hashes) {
	console.log(hashes);
});
```

Results in:
```
[{
  hash: 'c22zrgg',
  coverage: 1
  }, {
    hash: 'c22zrgu',
    coverage: 1
  }, {
    hash: 'c22zrgv',
    coverage: 1
  }, {
    hash: 'c22zrgy',
    coverage: 1
  }, {
    hash: 'c22zrgz',
    coverage: 1
  }, {
    hash: 'c23p25b',
    coverage: 1
  }, {
    hash: 'c22zrge',
    coverage: 1
  }, {
    hash: 'c22zrgs',
    coverage: 1
  }, {
    hash: 'c22zrgt',
    coverage: 1
  }, {
    hash: 'c22zrgw',
    coverage: 1
  }, {
    hash: 'c22zrgx',
    coverage: 1
  }, {
    hash: 'c23p258',
    coverage: 1
  }, {
    hash: 'c23p259',
    coverage: 1
  }, {
    hash: 'c23p25d',
    coverage: 1
  }, {
    hash: 'c22zrg7',
    coverage: 1
  }, {
    hash: 'c22zrgk',
    coverage: 1
  }, {
    hash: 'c22zrgm',
    coverage: 1
  }, {
    hash: 'c22zrgq',
    coverage: 1
  }, {
    hash: 'c22zrgr',
    coverage: 1
  }, {
    hash: 'c23p252',
    coverage: 1
  }, {
    hash: 'c23p253',
    coverage: 1
  }, {
    hash: 'c23p256',
    coverage: 1
  }, {
    hash: 'c22zrg5',
    coverage: 1
  }, {
    hash: 'c22zrgh',
    coverage: 1
  }, {
    hash: 'c22zrgj',
    coverage: 1
  }, {
    hash: 'c22zrgn',
    coverage: 1
  }, {
    hash: 'c22zrgp',
    coverage: 1
  }, {
    hash: 'c23p250',
    coverage: 1
  }, {
    hash: 'c23p251',
    coverage: 1
  }, {
    hash: 'c23p254',
    coverage: 1
  }]
```

Also, integer geohashes are available.
```javascript

geohashpoly({coords: polygon, precision: 7, hashMode: 'intersect', threshold: .2 }, function (err, hashes) {
  console.log(hashes);
});
```

Results in:
```
[ { hash: 'c22zru5', coverage: 0.10907589476779773 },
  { hash: 'c22zruh', coverage: 0.1833206866007423 },
  { hash: 'c22zruj', coverage: 0.18028863540246373 },
  { hash: 'c22zrun', coverage: 0.17725658397633032 },
  { hash: 'c22zrup', coverage: 0.371854519968465 },
  { hash: 'c23p2h0', coverage: 0.4366205168312899 },
  { hash: 'c23p2h1', coverage: 0.12629543815440486 },
  { hash: 'c23p2h4', coverage: 0 },
  { hash: 'c22zrgg', coverage: 0.5812073274274521 },
  { hash: 'c22zrgu', coverage: 1 },
  { hash: 'c22zrgv', coverage: 1 },
  { hash: 'c22zrgy', coverage: 1 },
  { hash: 'c22zrgz', coverage: 1 },
  { hash: 'c23p25b', coverage: 1 },
  { hash: 'c23p25c', coverage: 0.37848902360176456 },
  { hash: 'c23p25f', coverage: 0.10250388213616111 },
  { hash: 'c22zrge', coverage: 0.5709529397036096 },
  { hash: 'c22zrgs', coverage: 1 },
  { hash: 'c22zrgt', coverage: 1 },
  { hash: 'c22zrgw', coverage: 1 },
  { hash: 'c22zrgx', coverage: 1 },
  { hash: 'c23p258', coverage: 1 },
  { hash: 'c23p259', coverage: 1 },
  { hash: 'c23p25d', coverage: 0.9209610307039969 },
  { hash: 'c22zrg7', coverage: 0.5606985520190635 },
  { hash: 'c22zrgk', coverage: 1 },
  { hash: 'c22zrgm', coverage: 1 },
  { hash: 'c22zrgq', coverage: 1 },
  { hash: 'c22zrgr', coverage: 1 },
  { hash: 'c23p252', coverage: 1 },
  { hash: 'c23p253', coverage: 1 },
  { hash: 'c23p256', coverage: 0.9332695646194833 },
  { hash: 'c22zrg5', coverage: 0.2802101944420237 },
  { hash: 'c22zrgh', coverage: 0.508891052308125 },
  { hash: 'c22zrgj', coverage: 0.5116739844316215 },
  { hash: 'c22zrgn', coverage: 0.5144569167128468 },
  { hash: 'c22zrgp', coverage: 0.8135562497690018 },
  { hash: 'c23p250', coverage: 1 },
  { hash: 'c23p251', coverage: 0.6897629518261557 },
  { hash: 'c23p254', coverage: 0.5121017007737163 },
  { hash: 'c22zrfg', coverage: 0 },
  { hash: 'c22zrfu', coverage: 0 },
  { hash: 'c22zrfv', coverage: 0 },
  { hash: 'c22zrfy', coverage: 0 },
  { hash: 'c22zrfz', coverage: 0.027006766873594174 },
  { hash: 'c23p24b', coverage: 0.048726767963002855 },
  { hash: 'c23p24c', coverage: 0.01784468101138806 },
  { hash: 'c23p24f', coverage: 0 } ]
```

## Options

- `coords`: coordinate array for the geojson shape. **required**
- `precision`: geohash precision (eg. "gfjf1" is a precision 5 geohash).
- `rowMode`: allows for processing of geohashes by row.
- `hashMode`: defines filtering of returned geohashes. See below.
- `integerMode`: (true/false) Outputs integer versions of geohashes. Default precision for integer mode is 32 bits.


## hashMode

The `hashMode` option can be used to specify which hashes to return. Defaults to `'inside'`.

- `'inside'`: return hashes whose center points fall inside the shape.
- `'extent'`: return all hashes which make up the bounding box of the shape.
- `'intersect'`: return all hashes that intersect with the shape. Use the `'threshold'` option to specify a percentage of least coverage. See `examples/streaming.js`.

# Command Line

You can also install as a command line util via

`npm install geohash-poly-coverage -g`

## command line usage

Here are some examples of running it:

```bash
geohash-poly --csv=true --precision=9 --geocode=true  "[[[-122.350051, 47.702893 ], [-122.344774, 47.702877 ], [-122.344777, 47.70324 ], [-122.341982, 47.703234 ], [-122.341959, 47.701421 ], [-122.339749, 47.701416 ], [-122.339704, 47.69776 ], [-122.341913, 47.697797 ], [-122.341905, 47.697071 ], [-122.344576, 47.697084 ], [-122.344609, 47.697807 ], [-122.349999, 47.697822 ], [-122.350051, 47.702893 ]]]"
```

Where additional command line switches are

 - --csv=true output csv. default output is ndjson
 - --geocode=true to add the lat and lon to the output

All of the standard options are supported as well.




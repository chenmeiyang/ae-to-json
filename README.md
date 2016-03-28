# ae-to-json

[![experimental](http://badges.github.io/stability-badges/dist/experimental.svg)](http://github.com/badges/stability-badges)

The purpose of this module is to have one After Effects JSX (After Effects JS) script to:
- Export After Effects files as JSON like Objects
- Standardize After Effects to JSON Exporters
- Keep the exporter separate from renderer
- Create a platform on which renderers can be built on top of
- Have proper unit tests to catch breaking changes in After Effects (one failure point vs many failure points)

Documentation on Adobe's After Effects scripting guide can be found here:
- [After Effects Scripting Guide](http://download.macromedia.com/pub/developer/aftereffects/scripting/After-Effects-CS6-Scripting-Guide.pdf) _(appendix of API documentation starts on Page #12)_

## Usage

[![NPM](https://nodei.co/npm/ae-to-json.png)](https://www.npmjs.com/package/ae-to-json)

There are a few ways that you can consume this module. They are deocumented below:

- Usage with the after-effects module
- 

## Usage with the `after-effects` module

The [`after-effects`](https://www.npmjs.com/package/after-effects) allows you to run "functions" in After effects. Pass it one function it will return you the value in Node. No need to use the After Effects Scripting tools. To use with [`after-effects`](https://www.npmjs.com/package/after-effects):

Install `after-effects`:
```bash
$ npm i after-effects
```

In Javascript:
```javascript
var aeToJSON = require('ae-to-json/after-effects');
var ae = require('after-effects');

ae.execute(aeToJSON)
.then(function(json) {
  // do something with the json outout
})
.catch(function(e) {
  throw e;
});
```


## High Level Exported Format

Here's an example on an high level what will be exported from After Effects. These files can be very large since we attempt to export everything but at least this small bit of documentation will help you get started traversing the exports:
```javascript
{ 
  // meta data for the project
  project: {

    // items which are used in the Project panel
    // this includes: 
    //  Compositions, 
    //  Footage (images, videos, solids, etc.),
    //  Solids,
    //  etc.
    items: [
      // Composition Type
      {
        typeName: 'Composition',
        // these are the Compositions layers
        layers: [
          {
            // this includes all properties for this layer
            // properties are things like:
            //  Transform, (position, scale, rotation, anchor, etc.)
            //  Material Options,
            //  Effects,
            //  etc.
            properties: {

              // this is what the transform property would look like
              Transform: {

                // Transform's have their own properties
                // This is what X Position on a high level would look like
                "X Position": {

                  // if a property is animatable it will contain keyframes
                  // keyframes will be a two dimension array where each element
                  // on the second dimension is a key frame
                  "keyframes": [
                    // each key frame has a time (in seconds) which is at [0]
                    // a value which is at [1] (values can be scalar values 
                    // or arrays represented by arrays)
                    // 
                    // And an optional ease value. Generally you'd need more
                    // than one keyframe to have ease values.
                    // 
                    // It should be noted if no keyframes were added in After 
                    // Effects for ease of use one keyframe will be output with
                    // the value at time 0
                    [ time, value, ease ]
                  ]
                }
              }
            }
          }
        ]
      },
      {
        typeName: 'Footage'
      },
      {
        typeName: 'Folder',
        items: [
          // this items folder would contain
          // Compositions, Footage, Solids, etc.
        ]
      }
    ]
  }
}
```

## Export Example

You can view an export example at (be forewarned these files are huge):

[https://github.com/Jam3/ae-to-json/blob/master/example/example.json](https://github.com/Jam3/ae-to-json/blob/master/example/example.json)

## Testing

To run tests there are two scenarios. Run a test just once or watch the `src/`and `test/` folders and run tests. Below are the commands to run tests:

To run test once:
```bash
$ npm test
```

To watch and run (better for development):
```bash
$ npm start
```

When run unit tests are run on the JSON out along with a `testOutput.json` file is exported to the root director of this repo.

## License

MIT, see [LICENSE.md](http://github.com/jam3/ae-to-json/blob/master/LICENSE.md) for details.

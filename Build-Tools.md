# browserify

### Install
```sh
npm install tsify
```

### Using Command Line Interface

```sh
browserify main.ts -p [ tsify --noImplicitAny ] > bundle.js
```

### Using API

```javascript
var browserify = require("browserify");
var tsify = require("tsify");

browserify()
    .add('main.ts')
    .plugin('tsify', { noImplicitAny: true })
    .bundle()
    .pipe(process.stdout);
```

More details: [smrq/tsify](https://github.com/smrq/tsify)
### 

# grunt

### Install

```sh
npm install grunt-ts
```

### Basic Gruntfile.js

````javascript
module.exports = function(grunt) {
  grunt.initConfig({
    ts: {
      default : {
        src: ["**/*.ts", "!node_modules/**/*.ts"]
      }
    }
  });
  grunt.loadNpmTasks("grunt-ts");
  grunt.registerTask("default", ["ts"]);
};
````

More details: [TypeStrong/grunt-ts](https://github.com/TypeStrong/grunt-ts)

# gulp

### Install
```sh
npm install gulp-typescript
```

### Basic gulpfile.js

```javascript
var gulp = require("gulp");
var ts = require("gulp-typescript");

gulp.task("default", function () {
  var tsResult = gulp.src("src/*.ts")
    .pipe(ts({
        noImplicitAny: true,
        out: "output.js"
      }));
  return tsResult.js.pipe(gulp.dest('built/local'));
});
```
More details: [ivogabe/gulp-typescript](https://github.com/ivogabe/gulp-typescript)

# webpack

### Install
```sh
npm install awesome-typescript-loader --save-dev
```

### Basic webpack.config.js
```javascript
module.exports = {

  // Currently we need to add '.ts' to resolve.extensions array.
  resolve: {
    extensions: ['', '.ts', '.webpack.js', '.web.js', '.js']
  },

  // Source maps support (or 'inline-source-map' also works)
  devtool: 'source-map',

  // Add loader for .ts files.
  module: {
    loaders: [
      {
        test: /\.ts$/,
        loader: 'awesome-typescript-loader'
      }
    ]
  }
};
```
More details: [s-panferov/awesome-typescript-loader](https://github.com/s-panferov/awesome-typescript-loader)

# duo

### Install
```sh
npm install duo-typescript
```

### Using Command Line Interface

```sh
duo --use duo-typescript entry.ts
```

### Using API
```javascript
var Duo = require('duo');
var fs = require('fs')
var path = require('path')
var typescript = require('duo-typescript');

var out = path.join(__dirname, "output.js")

Duo(__dirname)
  .entry('entry.ts')
  .use(typescript())
  .run(function (err, results) {
    if (err) throw err;
    // Write compiled result to output file
    fs.writeFileSync(out, results.code);
  });
```
More details: [frankwallis/duo-typescript](https://github.com/frankwallis/duo-typescript)
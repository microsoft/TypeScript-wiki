## Browserify

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

## Grunt

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

## Gulp

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
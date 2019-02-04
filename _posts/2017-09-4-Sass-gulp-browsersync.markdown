---
title: "Sass + Gulp + browserSync"
tags: [sass gulp browserSync]
---

This is nothing, just workflow for sass :smile: . I am using Linux in this setup.
1. Install node js.
2. Open terminal, make a new directory, open it. <br>

```
mkdir sass-workflow
cd sass-workflow
```

{:start="3"}
3. Init package.json file.

```
npm init
```

Open package.json file. Change this line <br>

```
"scripts": {
    "test": ""
  }
 ``` 
 to this <br>
 ```
 "scripts": {
    "start": "gulp"
  }
  ```
  
{:start="4"}  
4. Install gulp, sass, and browserSync.

```
npm install gulp gulp-sass browser-sync --save-dev
```

{:start="5"}
5. In the root directory, create a folder and file for sass src/sass/style.scss. Write something in there, like this :smile:

```
$bg-color: teal;
body {
background-color: $bg-color;
}
```

{:start="6"}
6. In the root directory, create a new file called gulpfile.js. Write this for importing gulp, compile sass with gulp, and use auto reload for browser-sync.

```js
const gulp = require('gulp');
const browserSync = require('browser-sync').create();
const sass = require('gulp-sass');


//Compile sass
gulp.task('sass', function(){
    return gulp.src(['src/sass/*.scss'])
    .pipe(sass())
    .pipe(gulp.dest('src/css'))
    .pipe(browserSync.stream());
});

//Watch
gulp.task('serve', ['sass'], function(){
    browserSync.init({
        server: './src'
    });

    gulp.watch(['src/sass/*.scss'], ['sass']);
    gulp.watch(['src/*.html']).on('change', browserSync.reload);
});

//Default
gulp.task('default', ['serve']);

```

{:start="7"}
7. To run that file again install gulp-cli.

```
npm install -g gulp-cli
```

{:start="8"}
8. Create a new file in src/index.html. And write the basic html code that link with css.

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
<title>Sass Workflow</title>
<link rel="stylesheet" href="./css/style.css">
</head>
<body>   
</body>
</html>
```

{:start="9"}
9. Run gulp with

```
npm start
```

It will compiling the css file, run the browser automatically and use auto reload immediately when the change made. 


## 所有依赖安装完之后，在命令行直接敲
    
    gulp default
    就可以自动化运行了

### 下面是 gulpfile.js文件 的所有内容 
'use strict';
###加载 gulp 插件
    var gulp =  require('gulp'),
        cssnano = require('gulp-cssnano'),//css压缩
        concat = require('gulp-concat'),//文件合并
        uglify = require('gulp-uglify'),//js混淆
        del = require('del'),//清除
        runSequence = require('run-sequence'),//gulp 同步执行
        browserSync = require('browser-sync').create();//浏览器同步更新

##默认gulp 
###第一步清空文件 clear
###第二步构建文件 build
###第三步开启服务和监听文件 ['serve','watch']
    
    gulp.task('default',function(callback){
      return runSequence('clear','build',['serve','watch'],callback);
    });
    //第一步清空文件
    gulp.task('clear',function(callback){
      return del(['dist/'], callback);
    });
###第二步 同步执行 copy(文件复制) -> minijs(js混淆) -> minicss(css压缩) 按顺序来。
    gulp.task('build',function(callback){
      return runSequence('copy','miniJs','minicss',callback);
    });
    gulp.task('copy',function(){
      return gulp.src('src/**/*.*')
        .pipe(gulp.dest('dist/'));
    });
    gulp.task('miniJs', function() {
       return gulp.src(['src/**/*.js','!src/assets/**/*.*'])
        .pipe(uglify())
        .pipe(gulp.dest('dist/'));
    });
    gulp.task('minicss',function(){
      return gulp.src('src/css/*.css')
        .pipe(cssnano())
        .pipe(gulp.dest('dist/css/'));
    });
###开启本地服务器
    gulp.task('serve',function(){
       browserSync.init({
        server:{
          baseDir:'dist/'
        }
      })
    });
###监听文件变化后执行reload
    gulp.task('watch',function(){
       gulp.watch('src/**/*.*',['reload']);
    });
    gulp.task('reload',function(callback){
       runSequence('build','reload-browser',callback);
    });
    gulp.task('reload-browser',function(){
        browserSync.reload();
    });
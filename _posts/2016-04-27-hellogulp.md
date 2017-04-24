---
layout: post
title: "helloGulp"
description: "gulp 사용법"
tags: [gulp]
categories: [dev-tool]
---

# 필요한 사항
> [노드를 설치합니다.](https://nodejs.org/ko/ "node.js")
> 아래 내용은 모두 cmd 창에서 실행 합니다. 저는 windows7 64bit 운영체제 입니다.

# front end 빌드 순서
1. 코드 작성
2. javascript test
3. javascript minify
4. javascript merge
5. css minify
6. css merge
7. 결과물 폴더 deploy

>[이 블로그 내용을 참고 하였습니다.](http://programmingsummaries.tistory.com/356 "참고")

# 시작 
	npm init

> 결과적으로 package.json 파일이 생성이 됩니다.
> cmd 상에서 몇가지 사항을 질문합니다.
> 모든 내용은 package.json 파일에 기록 됩니다.
> gulp 와 gulp 에서 사용하는 plugin 설치 및 의존 관계는 package.json 파일에 기록 되는 것입니다.
> 차후에는 npm install 만 실행 하면, gulp와 gulp 관련 plugin이 자동으로 설치 됩니다.

# gulp setup
	npm install -g gulp // 이미 설치가 되었다면, 이 과정은 생략 합니다.

	npm install gulp --save-dev // gulp plugin은 해당 project에서만 필요 하므로, --save-dev 옵션을 줍니다.


# glup plugin setup
	npm install gulp-jshint jshint-stylish gulp-imagemin gulp-concat gulp-uglify gulp-minify-css gulp-usemin gulp-cache gulp-rev gulp-rename gulp-notify browser-sync del --save-dev
	
	
	npm install gulp-changed --save-dev


	npm install gulp-ng-annotate --save-dev

	npm install gulp-strip-debug --save-dev

	// 이건 사용하지 말자. 일단 잘 안된다. npm install gulp-minify-html --save-dev
	
	// 아래는 참고용
	// npm install gulp-angular-templatecache gulp-concat gulp-connect gulp-ng-annotate gulp-uglify --save-dev


# gulpfile.js 파일 작성
> sample 파일을 작성 해 두었습니다.

	var gulp = require('gulp'),
	    minifycss = require('gulp-minify-css'),
	    jshint = require('gulp-jshint'),
	    stylish = require('jshint-stylish'),
	    uglify = require('gulp-uglify'),
	    usemin = require('gulp-usemin'),
	    imagemin = require('gulp-imagemin'),
	    rename = require('gulp-rename'),
	    concat = require('gulp-concat'),
	    notify = require('gulp-notify'),
	    cache = require('gulp-cache'),
	    changed = require('gulp-changed'),
	    rev = require('gulp-rev'),
	    browserSync = require('browser-sync'),
	    del = require('del')
	    ngannotate = require('gulp-ng-annotate')
	;
	
	gulp.task('jshint', function() {
	  return gulp.src('app/scripts/**/*.js')
	  .pipe(jshint())
	  .pipe(jshint.reporter(stylish));
	});
	
	// Clean
	gulp.task('clean', function() {
	    return del(['dist']);
	});
	
	// Default task
	gulp.task('default', ['clean'], function() {
		gulp.start('usemin', 'imagemin','copyfonts');
	});
	
	gulp.task('usemin',['jshint'], function () {
		return gulp.src('./app/contactus.html')
				.pipe(usemin({
				css:[minifycss(),rev()],
				js: [ngannotate(),uglify(),rev()]
				}))
				.pipe(gulp.dest('dist/'));
	});
	
	// Images
	gulp.task('imagemin', function() {
		return del(['dist/images']), 
		gulp.src('app/images/**/*')
		.pipe(cache(imagemin({ optimizationLevel: 3, progressive: true, interlaced: true })))
		.pipe(gulp.dest('dist/images'))
		.pipe(notify({ message: 'Images task complete' }));
	});
	
	gulp.task('copyfonts', ['clean'], function() {
		gulp.src('./bower_components/font-awesome/fonts/**/*.{ttf,woff,eof,svg}*')
		.pipe(gulp.dest('./dist/fonts'));
		gulp.src('./bower_components/bootstrap/dist/fonts/**/*.{ttf,woff,eof,svg}*')
		.pipe(gulp.dest('./dist/fonts'));
	});
	
	// Watch
	gulp.task('watch', ['browser-sync'], function() {
		// Watch .js files
		gulp.watch('{app/scripts/**/*.js,app/styles/**/*.css,app/**/*.html}', ['usemin']);
		// Watch image files
		gulp.watch('app/images/**/*', ['imagemin']);
	});
	
	gulp.task('browser-sync', ['default'], function () {
		var files = [
			'app/**/*.html',
			'app/styles/**/*.css',
			'app/images/**/*.png',
			'app/scripts/**/*.js',
			'dist/**/*'
		];
	
		browserSync.init(files, {
			server: {
				baseDir: "dist",
				index: "contactus.html"
			}
		});
		// Watch any files in dist/, reload on change
		gulp.watch(['dist/**']).on('change', browserSync.reload);
	});


# 앞으로의 활용법
> gulp를 사용한다면, front end 코드 deploy를 graceful 하게 관리 할 수 있다.

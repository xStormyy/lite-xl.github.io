# [Lite XL][1] website

Work in progress
-
The website is built using [Jekyll][2]. [Node.js][3] is used to compile
all static assets including the [Bootstrap][4] library and built on
along with the [SASS][5] stylesheets. Most of the content on the website is
written using [Markdown][6].
Icons are provided by [Font Awesome][7], favicons by [Favicon Generator][8].
Language translations are provided using [JQuery Localize][9].

## Local Build Quick-start Guide

- Install the required dependencies: `ruby` and `yarn`
- Use the automatic setup via `site run`

or manually:

```sh
$ gem update
$ gem install bundler
$ yarn --no-bin-links
$ yarn dist
$ bundle exec jekyll serve --watch --host 0.0.0.0
```

See the [Wiki][10] for more details.

The local website should be available at <http://localhost:4000/>

[1]:  https://lite-xl.github.io/
[2]:  http://jekyllrb.com/
[3]:  https://nodejs.org/
[4]:  http://getbootstrap.com/
[5]:  https://sass-lang.com/dart-sass
[6]:  https://daringfireball.net/projects/markdown/
[7]:  http://fontawesome.io/
[8]:  https://realfavicongenerator.net/
[9]:  https://github.com/coderifous/jquery-localize/
[10]: https://github.com/lite-xl/lite-xl.github.io/wiki/

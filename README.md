# kearos-blog
[![Build Status](https://travis-ci.org/joostvdg/kearos-blog.svg?branch=master)](https://travis-ci.org/joostvdg/kearos-blog)

## To build

```
docker pull caladreas/mkdocs-docker-build-container
docker run -d -v /home/joost/Projects/github/kearos-blog:/usr/src/app caladreas/mkdocs-docker-build-container /bin/sh -c "mkdocs build"
```

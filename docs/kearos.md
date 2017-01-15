# Kearos
This is build with a docker build container; such as Jenkins and GitLab CI are now advocating.
The Travis CI will automatically upload this to the S3 bucket that will then host it as a static website!

## Static Website
For some documentation and tutorials, a static website is sufficient.
No need to increase the complexity or increase security risks but having a dynamic website.

Another bonus is that you can make use of tools that allow you to write documents in a nice format, without having to deal with the markup.
For example, this site uses MKDocs.

## MKDocs
This website uses [MKDocs](http://www.mkdocs.org/) and [MKDocs-Bootswatch](https://github.com/mkdocs/mkdocs-bootswatch) (for the theme).
This allows me to write the documentation and tutorials in simple Markdown.
MKDocs will generate a static website with styling and everything required from those markdown files and a single configuration file.

## Docker build container
To be able to utilize MKDocs some requirements have to be met.
MKDocs is a python tool that has other python dependencies.
I want to make sure that it always builds in the same way, no matter where I build the website (locally, Jenkins, Travis).
This gives rise to a need for a deterministic environment that can run virtually anywhere; Docker!

I've create a [Docker Build Container for MKDocs](https://github.com/joostvdg/mkdocs-docker-build-container) that is available in [Dockerhub](https://hub.docker.com/r/caladreas/mkdocs-docker-build-container/).
This will allow anyone with access to a docker daemon and the dockerhub to build this website withouth any other requirements.

## Travis CI
In order to execute the build and upload the result to a hosting environment, we need a build server.
There are many options available online, including many free ones.
I've opted for Travis CI simply because I saw it met my requirements: free for opensource, support for *docker build containers*, pipeline as code.

The configuration for Travis is very very simple:

```yaml
sudo: required
language: python
python:
- '2.7'
services:
- docker
before_install:
- docker pull caladreas/mkdocs-docker-build-container
- docker run -d --name buildcont -v ${TRAVIS_BUILD_DIR}:/usr/src/app caladreas/mkdocs-docker-build-container
  /bin/sh -c "mkdocs build"
- docker logs buildcont
script: pytest -h

deploy:
  provider: s3
  ...
  local-dir: site
  acl: public_read
  on:
    repo: joostvdg/kearos-blog
```

## AWS (Amazon)
Amazon Webservices allows a great many things for free.
One of which is S3 (Simple Storage Service), which allows you to host buckets (file folders).
These buckets also have the very nice option of allowing you let it host a static website.
Which is exactly what we needed to host this.

### Requirements

* add the website files to the bucket
* the bucket has "host static website" enabled
* make sure the files have public read access (see travis ci documentation)

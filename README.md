This is a fork and modification from this repository: https://github.com/oskapt/docker-hugo 

`freethebit/hugo` is a [Docker](https://www.docker.io) base image for static sites generated with [Hugo](http://gohugo.io).  

This image is uses the `extended` release of Hugo, which contains support for Sass/SCSS.

Images derived from this image can either run as a stand-alone server, or function as a volume image for your web server.  You can also use them in a CI/CD system such as Gitlab CI to build your content prior to bundling it into a standalone webserver container such as `httpd:alpine`.

# Prerequisites

The image is based on the following directory structure:

	.
	├── Dockerfile
	└── site
	    ├── config.toml
	    ├── content
	    │   └── ...
	    ├── layouts
	    │   └── ...
	    └── static
		└── ...

In other words, your Hugo site resides in the `site` directory, and you have a simple Dockerfile:

```
FROM freethebit/hugo 
```

# Gitlab users

In the file `.gitlab-ci.yml` use `image: freethebit/hugo:v0.55.4`, or `image: freethebit/hugo:latest`, but the current blackfriday (markdown engine) version that hugo uses have a nasty bug related to lists and code blocks, and this why I recomend using only `v0.55.4`.

You can check an example on how I use in my simple project in this [link](https://gitlab.com/freethebit/c/blob/f4292d04b895ec901011c7c4adf2daa3b17399ec/.gitlab-ci.yml), the content and theme are submodules, separed projects. 

# Building your site

Based on this structure, you can easily build an image for your site:

```
docker build -t freethebit/hugo .
```

Your site is automatically generated during this build. 


# Using your site

There are two options for using the image you generated: 

- as a stand-alone image
- as a volume image for your webserver

Using your image as a stand-alone image is the easiest:

```
docker run -p 1313:1313 freethebit/hugo
```

This will automatically start `hugo server`, and your blog is now available on http://localhost:1313. 

If you are using `boot2docker`, you need to adjust the base URL: 

```
docker run -p 1313:1313 -e HUGO_BASE_URL=http://YOUR_DOCKER_IP:1313 freethebit/hugo
```

The image is also suitable for use as a volume image for a web server, such as [nginx](https://registry.hub.docker.com/_/nginx/)

```
docker run -d -v /usr/share/nginx/html --name site-data freethebit/hugo
docker run -d --volumes-from site-data --name site-server -p 80:80 nginx
```


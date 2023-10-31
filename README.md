# Docker image for WebSVN 

Docker image for an easy installation of [WebSVN](https://websvnphp.github.io/),
which is a software that allows to display the content of one or multiple SVN
repositories on a web page.

Original Source code for this came from Detche on 
[GitHub](https://github.com/detche/docker-websvn).
  
I changed the config.php to allow downloads  

Example 1 demonstrates use with a rancid install in /var/lib/rancid  
  
I had to add read and execute persmissions for others on /var/lib/rancid/SVN for it to work  
  
I'm also using NGINX proxy_pass to provide password protection  
 

## Features

This image features:

* WebSVN served by an embedded HTTP server:
    * SVN repositories accessed via filesystem (docker mounts or volumes)
    * Customization of the 'About' message

WebSVN is configured using near-default values. Feel free to submit a PR in
order to add further configurability.

Refer to [WebSVN's website](https://websvnphp.github.io/) for the full list of
features of the software itself.

## Non-features

This image serves as a SVN repository viewer only, it does not
include a full SVN server installation nor does it allow participation on the
underlying repositories.

Additionally, those features will not be added to this image:

* HTTPS
* Authentication/authorization

Those are best handled externally, refer to [samples](#Samples) for examples.
## Docker Compose

My preferred way to manage docker is through docker-compose   
  
To build: docker-compose build --force-rm --no-cache  
  
To start: docker-compose up -d  
 * the compose file has the image restarting automatically  
  
To remove: docker-compose down --rmi all --volumes  
* this erases the docker instances so it will build the next time it's started  

  

## Docker Run style Usage

Build the image first
```
docker build -t websvn .
```

Mount the individual SVN repositories under path `/repos` within container.
WebSVN is configured to expose all of its subfolders as repositories.

### Example: Rancid Repository

```
docker run --rm -p 8008:80 \
    -e ABOUT_MESSAGE="My Rancid Configs" \
    -v /var/lib/rancid/SVN:/repos/Rancid:ro \
    websvn
```

Your repository would be available as `PUBLIC_NAME` on the WebSVN interface.

### Example: multiple repositories

```
docker run --rm -p 8008:80 \
    -e ABOUT_MESSAGE="Welcome to our SVN repositories." \
    -v /path/to/your/first/repo:/repos/REPO1:ro \
    -v /path/to/your/second/repo:/repos/REPO2:ro \
    websvn 
```

### Example: exposing directly the parent of one to multiple repositories

```
docker run --rm -p 8008:80 \
    -e ABOUT_MESSAGE="Welcome to our SVN repositories." \
    -v /home/svn/repos:/repos:ro \
    websvn 
```

All repositories within `/home/svn/repos` would be published identified by their
folder name.

Because the `/repos` folder is pre-created within the image, you also can use
the `--mount` flag:

```
docker run --rm -p 8008:80 \
    -e ABOUT_MESSAGE="Welcome to our SVN repositories." \
    --mount type=bind,source=/home/svn/repos,target=/repos,readonly \
    detche/websvn:2.8.1 
```

## Samples

Sample(s) on how to add HTTPS and/or authentication above this image:

* [HTTPS via Caddy automatic certificates](https://github.com/detche/docker-websvn/tree/master/samples/https)
* [HTTPS + Authentication via Caddy, Caddy Security and Azure AD](https://github.com/detche/docker-websvn/tree/master/samples/auth-caddy-azure)


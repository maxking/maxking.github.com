---
title: "HOWTO: Release a signed docker image"
date: "2017-07-03 20:17:23"
type: "post"
---


# HOWTO: Release a signed docker image
First, install notary from one of the released versions.

Then, you need to build a new image with a specific tag, lets say you are building an image called `maxking/mailman-web` and you decide to sign the `testing` tag:


    $ docker build --disable-cotent-trust=false -t maxking/mailman-web:latest .

The above command will build the image and then ask you to initialize a few keys if you haven’t yet created the keys. There will be in total three keys,

- one root key which will be kept offline and is the main source of trust for all your notary repositories. Use a secure passphrase for this one.
- one target key which will correspond to the specific repository that you will be signing, `docker.io/maxking/mailman-web` in this case
- one snapshot key which is….

So, after you are done with building the images, you can now push the image to Docker Hub along with the signature to `notary.docker.io` which is the default notary server.


    $ docker push --disable-content-trust=false maxking/mailman-web:testing

From a different client, to pull the images and verify the signature run the following command:


    $ docker pull --disable-content-trust=false maxking/mailman-web:testing

This command will fail if either the signature verification process doesn’t succeed because either the signature is not correct or not present.




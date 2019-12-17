
# Docker Image für Podman

~~~
$ cd podman/
$ docker build -t podman:latest .
$ docker tag podman:latest phoswald.ch:5000/podman:latest
$ docker login phoswald.ch:5000
$ docker push phoswald.ch:5000/podman:latest
~~~

Aber Podman in Docker scheint nicht zu gehen (jedenfalls mit overlayfs):

~~~
$ docker run -it --rm phoswald.ch:5000/podman podman run -it opensuse/leap
ERRO[0000] 'overlay' is not supported over overlayfs    
Error: error creating libpod runtime: operation not permitted
~~~

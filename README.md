
# openanalytics/r-ver Docker Images

To pull the image for a particular version of R from Docker Hub (as made by the Dockerfiles in this repository),
use the R version as the tag, e.g. for R version 3.5.3

```
sudo docker pull openanalytics/r-ver:3.5.3
```

The relevant Docker Hub repository can be found at

https://hub.docker.com/r/openanalytics/r-ver

with a folder for each version of R.

To build the image from the Dockerfile, navigate into the directory for a particular version (e.g. 3.5.3)

```
cd 3.5.3
```

and run 

```
sudo docker build -t openanalytics/r-ver:3.5.3 .
```

(c) Copyright Open Analytics NV, 2019.

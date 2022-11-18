
# openanalytics/r-ver Docker Images

## Add an image for a particular (new) R version

- add a new **choice** in the
  [Jenkinsfile](https://scm.openanalytics.eu/git/r-ver/src/branch/master/Jenkinsfile#L34) specifying
  the version
- add a **folder** with the new R version e.g. 4.2.2
- include a lightweight Dockerfile in the newly created folder with
```
FROM rocker/r-ver:4.2.2
ENV R_PAPERSIZE=a4
```
- the build will automatically be triggered but not for the latest version (you can cancel it)
- to trigger the right build (for the latest and greatest), trigger the parametrized build for the
  version of your choice on [Jenkins](https://ci.openanalytics.eu/job/git/job/r-ver/job/master/)

## Rebuild an image

Trigger the parametrized build at

https://ci.openanalytics.eu/job/git/job/r-ver/job/master/

## Pull an image

To pull the image for a particular version of R from Docker Hub (as made by the Dockerfiles in this repository),
use the R version as the tag, e.g. for R version 3.5.3

```
sudo docker pull openanalytics/r-ver:3.5.3
```

The relevant Docker Hub repository can be found at

https://hub.docker.com/r/openanalytics/r-ver

with a folder for each version of R.

## Build an image manually 

To build the image from the Dockerfile, navigate into the directory for a particular version (e.g. 3.5.3)

```
cd 3.5.3
```

and run 

```
sudo docker build -t openanalytics/r-ver:3.5.3 .
```

(c) Copyright Open Analytics NV, 2019-2023.

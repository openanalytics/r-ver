
# openanalytics/r-ver Docker Images

## Add an image for a particular (new) R version

- add a new **choice** in the
  [Jenkinsfile](https://scm.openanalytics.eu/git/r-ver/src/branch/master/JenkinsfileSCM#L10) specifying
  the version

If using the default Dockerfile, no further changes is needed, otherwise:

- add a **folder** with the new R version e.g. 4.4.1
- include a Dockerfile in the newly created folder based on the below example

```
FROM rocker/r-ver:4.4.1
ENV R_PAPERSIZE=a4
# add more lines that are different from default
```

**Note:** the build will *not* be automatically triggered, so you need to manually launch the parametrized build for the version of your choice on [Jenkins](https://ci.openanalytics.eu/job/scm-openanalytics/job/r-ver/job/master/)

## Rebuild an image

Trigger the parameterized build at

https://ci.openanalytics.eu/job/scm-openanalytics/job/r-ver/job/master/

## Pull an image

To pull the image for a particular version of R from Docker Hub (as made by the Dockerfiles in this repository),
use the R version as the tag, e.g. for R version 4.4.1

```
sudo docker pull openanalytics/r-ver:4.4.1
```

The relevant Docker Hub repository can be found at

https://hub.docker.com/r/openanalytics/r-ver

with a folder for each version of R.

## Build an image manually 

To build the image from the default Dockerfile, run

```
sudo docker build -t openanalytics/r-ver:4.4.1 --build-arg R_VERSION=4.4.1 .
```

To build the image from an 'exceptional' Dockerfile, navigate into its directory first (e.g. 3.5.3)

```
cd 3.5.3
```

and run 

```
sudo docker build -t openanalytics/r-ver:3.5.3 .
```

(c) Copyright Open Analytics NV, 2019-2026.

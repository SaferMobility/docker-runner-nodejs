[![Build Status](http://dockeri.co/image/safermobility/docker-runner-nodejs)](https://registry.hub.docker.com/u/safermobility/docker-runner-nodejs/)

# Introduction

A CI runner for NodeJS.

Built on top of the [sameersbn/gitlab-ci-runner](https://github.com/sameersbn/docker-gitlab-ci-runner) base image.

Since we inherit the [sameersbn/gitlab-ci-runner](https://github.com/sameersbn/docker-gitlab-ci-runner) base image, we also inherit its runtime. This means we only have to setup the image to satisfy the project's build requirements.

All package installations are performed in the `Dockerfile` while system configuration is performed in the `install` script.

# Installation

Pull the latest version of the image from the docker index.

```bash
docker pull safermobility/runner-gitlab-nodejs:latest
```

Alternately you can build the image yourself.

```bash
git clone https://github.com/safermobility/docker-runner-nodejs.git
cd docker-runner-nodejs
docker build --tag="$USER/runner-gitlab-nodejs" .
```

# Quick Start
For a runner to do its trick, it has to first be registered/authorized on the GitLab CI server. This can be done by running the image with the **app:setup** command.

```bash
mkdir -p /opt/runner-gitlab-nodejs
docker run --name runner-gitlab-nodejs -i -t --rm \
	-v /opt/runner-gitlab-nodejs:/home/gitlab_ci_runner/data \
  safermobility/runner-gitlab-nodejs:latest app:setup
```

The command will prompt you to specify the location of the GitLab CI server and provide the registration token to access the server. With this out of the way the image is ready, lets get is started.

```bash
docker run --name runner-gitlab-nodejs -d \
	-v /opt/runner-gitlab-nodejs:/home/gitlab_ci_runner/data \
	safermobility/runner-gitlab-nodejs:latest
```

You now have the runner to perform continuous integration with NodeJS.

# Data Store
GitLab CI Runner saves the configuration for connection and access to the GitLab CI server. In addition, SSH keys are generated as well. To make sure this configuration is not lost when when the container is stopped/deleted, we should mount a data store volume at

* /home/gitlab_ci_runner/data

Volumes can be mounted in docker by specifying the **'-v'** option in the docker run command.

```bash
mkdir /opt/runner-gitlab-nodejs
docker run --name runner-gitlab-nodejs -d -h runner-gitlab.local.host \
  -v /opt/runner-gitlab-nodejs:/home/gitlab_ci_runner/data \
  safermobility/runner-gitlab-nodejs:latest
```

# Shell Access

For debugging and maintenance purposes you may want access the containers shell. If you are using docker version `1.3.0` or higher you can access a running containers shell using `docker exec` command.

```bash
docker exec -it runner-gitlab bash
```
## Upgrading

To update the runner, simply stop the image and pull the latest version from the docker index.

```bash
docker stop runner-gitlab-nodejs
docker pull safermobility/runner-gitlab-nodejs:latest
docker run --name runner-gitlab-nodejs -d [OPTIONS] safermobility/runner-gitlab-nodejs:latest
```

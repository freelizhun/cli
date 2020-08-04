[![build status](https://circleci.com/gh/docker/cli.svg?style=shield)](https://circleci.com/gh/docker/cli/tree/master)
[![Build Status](https://ci.docker.com/public/job/cli/job/master/badge/icon)](https://ci.docker.com/public/job/cli/job/master)

docker/cli
==========

This repository is the home of the cli used in the Docker CE and
Docker EE products.

Development
===========

`docker/cli` is developed using Docker.

Build a linux binary:

```
$ make -f docker.Makefile binary

也就是：
root@master1:/home/kylin/k8s/docker-cli/src/github.com/docker/cli# make -f docker.Makefile binary
# build dockerfile from stdin so that we don't send the build-context; source is bind-mounted in the development environment
cat ./dockerfiles/Dockerfile.binary-native | docker build --build-arg=GO_VERSION -t docker-cli-native -
Sending build context to Docker daemon  2.048kB
Step 1/7 : ARG GO_VERSION=1.13.14
Step 2/7 : FROM    golang:${GO_VERSION}-alpine
 ---> 298aa35a5d8d
Step 3/7 : RUN     sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories
 ---> Running in 907732f10c99
Removing intermediate container 907732f10c99
 ---> 0b5f4b1653fe
Step 4/7 : RUN     apk add -U git bash coreutils gcc musl-dev
 ---> Running in 60d0f9719f33
fetch http://mirrors.aliyun.com/alpine/v3.12/main/x86_64/APKINDEX.tar.gz
fetch http://mirrors.aliyun.com/alpine/v3.12/community/x86_64/APKINDEX.tar.gz
(1/24) Installing ncurses-terminfo-base (6.2_p20200523-r0)
(2/24) Installing ncurses-libs (6.2_p20200523-r0)
(3/24) Installing readline (8.0.4-r0)
(4/24) Installing bash (5.0.17-r0)
Executing bash-5.0.17-r0.post-install
(5/24) Installing libacl (2.2.53-r0)
(6/24) Installing libattr (2.4.48-r0)
(7/24) Installing coreutils (8.32-r0)
(8/24) Installing libgcc (9.3.0-r2)
(9/24) Installing libstdc++ (9.3.0-r2)
(10/24) Installing binutils (2.34-r1)
(11/24) Installing gmp (6.2.0-r0)
(12/24) Installing isl (0.18-r0)
(13/24) Installing libgomp (9.3.0-r2)
(14/24) Installing libatomic (9.3.0-r2)
(15/24) Installing libgphobos (9.3.0-r2)
(16/24) Installing mpfr4 (4.0.2-r4)
(17/24) Installing mpc1 (1.1.0-r1)
(18/24) Installing gcc (9.3.0-r2)
(19/24) Installing nghttp2-libs (1.41.0-r0)
(20/24) Installing libcurl (7.69.1-r0)
(21/24) Installing expat (2.2.9-r1)
(22/24) Installing pcre2 (10.35-r0)
(23/24) Installing git (2.26.2-r0)
(24/24) Installing musl-dev (1.1.24-r9)
Executing busybox-1.31.1-r16.trigger
OK: 160 MiB in 39 packages
Removing intermediate container 60d0f9719f33
 ---> 675ef3aff375
Step 5/7 : ENV     CGO_ENABLED=0         DISABLE_WARN_OUTSIDE_CONTAINER=1
 ---> Running in 753d13b5eea0
Removing intermediate container 753d13b5eea0
 ---> 3388751a8fae
Step 6/7 : WORKDIR /go/src/github.com/docker/cli
 ---> Running in 54a2087579e3
Removing intermediate container 54a2087579e3
 ---> ae4a71d3ae9c
Step 7/7 : CMD     ./scripts/build/binary
 ---> Running in d4e88ec8eb7f
Removing intermediate container d4e88ec8eb7f
 ---> 54d85d503e0d
Successfully built 54d85d503e0d
Successfully tagged docker-cli-native:latest
docker run --rm -e VERSION=20.03.0-dev -e GITCOMMIT -e PLATFORM -e TESTFLAGS -e TESTDIRS -e GOOS -e GOARCH -e GOARM -e TEST_ENGINE_VERSION= -v "/home/kylin/k8s/docker-cli/src/github.com/docker/cli":/go/src/github.com/docker/cli -v "docker-cli-dev-cache:/root/.cache/go-build"  docker-cli-native
Building statically linked build/docker-linux-amd64

最终在目录build/得到docker二进制可执行客户端文件
root@master1:/home/kylin/k8s/docker-cli/src/github.com/docker/cli# ls -al build/
total 52704
drwxr-xr-x  2 root root     4096 Aug  4 11:52 .
drwxr-xr-x 19 root root     4096 Aug  4 11:52 ..
lrwxrwxrwx  1 root root       18 Aug  4 11:52 docker -> docker-linux-amd64
-rwxr-xr-x  1 root root 53959153 Aug  4 11:52 docker-linux-amd64


整个过程由docker.Makefile->./dockerfiles/Dockerfile.binary-native->./scripts/build/binary->./scripts/build/.variables

由docker.Makefile文件中的：
.PHONY: build_binary_native_image
build_binary_native_image:
	# build dockerfile from stdin so that we don't send the build-context; source is bind-mounted in the development environment
	cat ./dockerfiles/Dockerfile.binary-native | docker build --build-arg=GO_VERSION -t $(BINARY_NATIVE_IMAGE_NAME) -
 
 以及：
 binary: build_binary_native_image ## build the CLI
	$(DOCKER_RUN) $(BINARY_NATIVE_IMAGE_NAME)
 部分
 
生成了docker-cli-native:latest镜像，再运行该镜像来编译docker（cmd/docker/docker.go）二进制文件
```



Build binaries for all supported platforms:

```
$ make -f docker.Makefile cross
```

Run all linting:

```
$ make -f docker.Makefile lint
```

List all the available targets:

```
$ make help
```

### In-container development environment

Start an interactive development environment:

```
$ make -f docker.Makefile shell
```

In the development environment you can run many tasks, including build binaries:

```
$ make binary
```

Legal
=====
*Brought to you courtesy of our legal counsel. For more context,
please see the [NOTICE](https://github.com/docker/cli/blob/master/NOTICE) document in this repo.*

Use and transfer of Docker may be subject to certain restrictions by the
United States and other governments.

It is your responsibility to ensure that your use and/or transfer does not
violate applicable laws.

For more information, please see https://www.bis.doc.gov

Licensing
=========
docker/cli is licensed under the Apache License, Version 2.0. See
[LICENSE](https://github.com/docker/docker/blob/master/LICENSE) for the full
license text.

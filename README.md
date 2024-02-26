![Build status](https://github.com/jasonn3/build-container-installer/actions/workflows/build-and-test.yml/badge.svg??event=push)

# Bulid Container Installer Action
This action is used to generate an ISO for installing OSTrees stored in a container. This utilizes the anaconda command `ostreecontainer`

## Makefile
A Makefile is provided for ease of use. There are separate targets for each file generated, however `make` can be used to generate the final image and `make clean` can be used to clean up the workspace. The resulting ISO will be stored in the `build` directory.

See [Customizing](#customizing) for information about customizing the image that gets created.

## Container
A container with the necessary tools already installed is provided at `ghcr.io/jasonn3/build-container-installer:latest`

To use the container file, run `docker run --privileged --volume .:/build-container-installer/build ghcr.io/jasonn3/build-container-installer:latest`.

This will create an ISO with the baked in defaults of the container image.

See [Customizing](#customizing) for information about customizing the image that gets created. The variable can either be defined as environment variables or as command arguments.
Examples:

Building an ISO to install Fedora 38
```bash
docker run --rm --privileged --volume .:/build-container-installer/build -e VERSION=38 -e IMAGE_NAME=base -e IMAGE_TAG=38 -e VARIANT=Server ghcr.io/jasonn3/build-container-installer:latest
```

Building an ISO to install Fedora 39
```bash
docker run --rm --privileged --volume .:/build-container-installer/build -e VERSION=39 -e IMAGE_NAME=base -e IMAGE_TAG=39 -e VARIANT=Server ghcr.io/jasonn3/build-container-installer:latest
```

## Customizing
The following variables can be used to customize the create image.

| Variable          | Description                                              | Default Value                  |
| ----------------- | -------------------------------------------------------- | ------------------------------ |
| ARCH              | Architecture for image to build                          | x86_64                         |
| VERSION           | Fedora version of installer to build                     | 39                             |
| IMAGE_REPO        | Repository containing the source container image         | quay.io/fedora-ostree-desktops |
| IMAGE_NAME        | Name of the source container image                       | base                           |
| IMAGE_TAG         | Tag of the source container image                        | *VERSION*                      |
| EXTRA_BOOT_PARAMS | Extra params used by grub to boot the anaconda installer | \[empty\]                      |
| VARIANT           | Source container variant\*                               | Server                         |
| WEB_UI            | Enable Anaconda WebUI (experimental)                     | false                          |

Available options for VARIANT can be found by running `dnf provides system-release`. 
Variant will be the third item in the package name. Example: `fedora-release-kinoite-39-34.noarch` will be kinoite

## VSCode Dev Container
There is a dev container configuration provided for development. By default it will use the existing container image available at `ghcr.io/jasonn3/build-container-installer:latest`, however, you can have it build a new image by editing `.devcontainer/devcontainer.json` and replacing `image` with `build`. `Ctrl+/` can be used to comment and uncomment blocks of code within VSCode.

The code from VSCode will be available at `/workspaces/build-container-installer` once the container has started.

Privileged is required for access to loop devices for lorax.

Use existing image
```json
{
	"name": "Existing Dockerfile",
	// "build": {
	// 	"context": "..",
	// 	"dockerfile": "../Dockerfile",
	// 	"args": {
	// 		"version": "39"
	// 	}
	// },
	"image": "ghcr.io/jasonn3/build-container-installer:latest",
	"overrideCommand": true,
	"shutdownAction": "stopContainer",
	"privileged": true
}
```

Build a new image
```json
{
	"name": "Existing Dockerfile",
	"build": {
		"context": "..",
		"dockerfile": "../Dockerfile",
		"args": {
			"version": "39"
		}
	},
	//"image": "ghcr.io/jasonn3/build-container-installer:latest",
	"overrideCommand": true,
	"shutdownAction": "stopContainer",
	"privileged": true
}
```
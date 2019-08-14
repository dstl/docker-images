# REMEDI Docker

This project contains the necessary Dockerfiles, Docker Compose configuration, and scripts to run a [REMEDI](https://github.com/ivan-zapreev/Distributed-Translation-Infrastructure/) cluster with Docker. You will need to provide your own configuration, models and processing tools.

## Dockerfile Structure

The Dockerfiles in this project are based on a common base image which contains all the necessary tools, with additional layers on top to configure for each specific REMEDI tool.

* Remedi - This is the base image, which installs the necessary dependencies and builds the REMEDI suite of tools. Must be tagged as `remedi` for the other images to find it.
* Server - This image is built on top of the Remedi image, and contains customisations for the Translation server. It exposes the Translation server on port 9001, although this can be changed with the `port` argument when building the image.
* Processor - This image is built on top of the Remedi image, and contains customisations for the pre- and post-processing servers. It exposes the Processor server on port 9003, although this can be changed with the `port` argument when building the image.
* Balancer - This image is built on top of the Remedi image, and contains customisations for the Load Balancer. It exposes the Load Balancer on port 9005, although this can be changed with the `port` argument when building the image.

The default version of REMEDI is 1.8.2. If a different version is required, this can be set with the `version` argument when building the base image.

## Configuration, Models and Tools

Each server needs configuration passed to it, and in the case of the Translation server the MT models as well. Optionally, for the Processor servers you can also pass through tools and scripts to be used for the processing.

### Configuration

Each server looks for configuration at the following locations:

* Translation Server - `/opt/conf/server.cfg`
* Processor Server - `/opt/conf/processor.cfg`
* Load Balancer - `/opt/conf/balancer.cfg`

These should be passed through via the Docker run command, for example:

    docker run -d -v conf/balancer.cfg:/opt/conf/balancer.cfg -p 9005:9005 remedi-balancer:latest

The configuration files should be valid REMEDI configuration files, as described in the REMEDI documentation. SSL support is enabled should you wish to use it.

If changing the port within the configuration, be sure to rebuild the images with the `port` argument set correctly.

### Models

A volume is created in the Translation Server image, `/opt/models/` for you to place your models. You should include this volume in your Docker run command. The exact model names will be specified in your `server.cfg`. For example:

    docker run -d -v models/:/opt/models/ -v conf/server.cfg:/opt/conf/server.cfg -p 9001:9001 remedi-server:latest

### Tools

A volume is created on the Processor Server image, `/opt/tools/` for you to optionally place processing tools and scripts in. You can include this volume in your Docker run command. The exact model names will be specified in your `processor.cfg`. For example:

    docker run -d -v tools/:/opt/tools/ -v conf/processor.cfg:/opt/conf/processor.cfg -p 9003:9003 remedi-processor:latest

## Running the REMEDI Cluster

An example Docker Compose script is provided (`example/docker-compose.yml`) to demonstrate how to create a REMEDI Cluster. It should be used as a starting point for creating your own scripts, rather than running it directly out of the box. However, the example directory is setup to be run directly if you wish to demonstrate that REMEDI can be run.

    cd example/
    docker-compose up -d --build

Prior to running the Docker Compose script, you must build the base image, or have it available in a local image repository.

    docker build --tag=remedi . 

## Example Models and Tools

No models are provided with the example, however you can download sample models from the [REMEDI GitHub example](https://github.com/ivan-zapreev/Distributed-Translation-Infrastructure/tree/master/demo/no-tls/models).
Note that the REMEDI project is under a GPLv3 license, as are the models.
The models should only be used to validate that REMEDI will run, and you should not expect them to perform any meaningful translation.

The models should be placed in the `models/de` and `models/fr` folder within the example directory, with the following names:

* `model.lm` - For the language model
* `model.rm` - For the reordering model
* `morel.tm` - For the translation model

Similarly, no pre- or post-processing scripts are provided but example scripts can be found within the [REMEDI source code](https://github.com/ivan-zapreev/Distributed-Translation-Infrastructure/tree/master/script/text).
The example is configured to expect a `tools/pre_process.sh` and `tools/post_process.sh` in the example directory, but be aware that these files may have additional dependencies themselves.

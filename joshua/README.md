# Joshua Docker

This project creates a Docker image allowing you to run [Apache Joshua](https://joshua.apache.org) easily. Joshua is built from the latest master branch on [GitHub](https://github.com/apache/incubator-joshua).

## Building Joshua

To build the Joshua image, run the following command from the project directory:

    docker build . -t joshua

Optionally, you can set a default amount of RAM to provide to Joshua when it runs. If not set, then 8G is used.

    docker build . -t joshua --build-arg mem=12G

## Running Joshua

To run Joshua, you will need to provide a translation model and configuration file.
These are not included with this project, but pretrained models and configurations (collectively referred to as language packs) are available from the [Joshua website](https://cwiki.apache.org/confluence/display/JOSHUA/Language+Packs).

To use these, download the language pack and extract it. There will be a folder called `model` and a configuration file called `joshua.config`. These are the files you need, the rest can be discarded.

Once you have these files, you can run the Joshua Docker image as follows:

    docker run -d -v /full/path/to/my-model/:/opt/bin/joshua/model/ -v /full/path/to/joshua.config:/opt/bin/joshua/joshua.config -p 5674:5674 joshua:latest

Note that the local model (`/full/path/to/my-model` in the example above) must be mounted to `/opt/bin/joshua/model/`, and the config must be mounted as `/opt/bin/joshua/joshua.config`.

### Example

As an example, we will look at translating German to English, so start by downloading the [relevant language pack](https://www.dropbox.com/sh/3uag3az9imyih0x/AAABtXI87ldqGxYOvBiHSbt_a/apache-joshua-de-en-2016-11-18.tgz?dl=0) and extracting it.

    tar -xvzf apache-joshua-de-en-2016-11-18.tgz

Once extracted there will be a folder called `model` and a configuration file called `joshua.config`. These are the files you need, the rest can be discarded.

Once you have these files, you can run the Joshua Docker image as follows:

    docker run -d -v ${PWD}/apache-joshua-de-en-2016-11-18/model/:/opt/bin/joshua/model/ -v ${PWD}/apache-joshua-de-en-2016-11-18/joshua.config:/opt/bin/joshua/joshua.config -p 5674:5674 joshua:latest

You should now have Joshua running in a Docker container in a daemon process. We can check it's running by sending a CURL command to the REST interface:

    curl "localhost:5674/translate?q=ich%20wohne%20gerade%20in%20einer%20anstalt"

If all is well, you should get the following response back:

    {
      "data": {
        "translations": [
          {
            "translatedText": "I\u0027m just in an institution",
            "raw_nbest": [
              {
                "hyp": "i \u0027m just in an institution",
                "totalScore": -5.943904
              }
            ]
          }
        ]
      },
      "metadata": []
    }


## Accessing Joshua

The Joshua Docker image will run the Joshua web server on port `5674`. You can submit REST commands to this server, as described in the [Joshua documentation](https://cwiki.apache.org/confluence/display/JOSHUA/RESTful+API).

For example:

    curl "localhost:5674/translate?meta=list_weights&q=Bonjour%20le%20monde"

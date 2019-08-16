# Moses Docker


## Setup

You can build the image using the following command.

    docker build -t 'moses-server' .

## Models

To run the image you'll need to train or download a pre-trained moses model.

Training falls outside of the scope of this document, pre-trained models can
be found on the [Moses website](http://www.statmt.org/moses/RELEASE-4.0/models/)

The core of the model is the 'moses.ini' file. This contains all the parameters
and file paths for the model. The statmt pretrained models have multiple ini files
for different iterations of their training. Download the most recent one, 
and then download any files referenced within it. Finally you'll need to update
the paths in the ini to assume all files are located at '/mnt/model'. You'll
also need to remove the version number from the end of the ini.

Unfortunately this process is entirely manual.
Fortunately it only needs to be carried out once. 

Once complete, the image can be run as follows:

    docker run -v /path/to/model/files:/mnt/model -p 8080:8080 moses-server

## Using the server

Moses exposes an XML-RPC service on port 8080.

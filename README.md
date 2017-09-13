# tfserving
Build docker images to serve tensorflow models in production. [TensorFlow Serving](https://github.com/tensorflow/serving) is an open-source library for serving machine learning models in production.

### Two different model servers
See the section [Installing the ModelServer](https://www.tensorflow.org/serving/setup).

The images we have hosted on epigram docker hub are built with the Dockerfiles in *build-light/* and *build-light-universal/*
 
- In *build-light/* there is a Dockerfile to build an image with a compiled server. This server might not work on all servers, it's compiled with AVX instructions.
- In *build-light-universal/* there is a Dockerfile to build an image without AVX instructions. Should work on all servers.

See the Dockerfiles for build information.

If you want to serve the mnist model in one of these images, do:

`docker run -it --name mnist -p 9000:9000 -v /Users/slp/.tfwrapper/serving_models:/models/ epigramai/model-server:light --port=9000 --model_name=mnist --model_base_path=/models/mnist`

- We name the container mnist
- We're mapping port 9000 from outside the container to the model server's port 9000.
- We mount a volume where the models are located (the -v HOST_PATH:WHERE_TO_MOUNT_IN_CONTAINER)
- Three flags (port, name, base path) to tell the model what to run and which port.


#### build-model-light
Although we can easily use the images we built and ran in the previous section, we can make the run process even easier.

In *build-model-light/* there is a Dockerfile that COPY the model into the image. By using this approach, the models
are stored inside the image and there's no need to use -v option and passing all the flags (e.g --port=9000, ...) to the container
when running it. The image is ready to run as is.

Assume you have build a container called *epigramai/model-server:my-model-1*, then serve the model by doing:
`docker run -it --name mnist -p 9000:9000 epigramai/model-server:my-model-1`

Se the Dockerfile in *build-model-light/* for how to build. 

### Client to send requests
TODO...
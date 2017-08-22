# tfserving
Build docker images to serve tensorflow models in production. [TensorFlow Serving](https://github.com/tensorflow/serving) is an open-source library for serving machine learning models in production.

### docker-build (with apt-get tensorflow-model-server)
See the section [Installing the ModelServer](https://www.tensorflow.org/serving/setup).

This is a lightweight model server. The images we have hosted on epigram docker hub are built with the files in *build-light/* and *build-light-universal/*
 
- In *build-light/* there is a Dockerfile to build an image with a compiled server. This server might not work on all servers, it's compiled with AVX instructions.
- In *build-light-universal/* there is a Dockerfile to build an image without AVX instructions. Should work on all servers.

### docker-build (compile model server) *Deprecated see prev section*
Dockerfile.devel is copied from TensorFlow Serving github repo. It's basically used to build a docker image that has all necessary dependencies installed to build and run the code to serve models.

TensorFlow Serving code is built with [bazel](https://bazel.build/). If you look in Dockerfile.devel, you'll see that a bunch of bazel stuff if installed in order to build and run TensorFlow Serving code.

#### Build docker image
- `cd docker-build/`
- `docker build -t REPONAME -f Dockerfile.devel .`
 
 You've now built an image with all the dependencies to build and run Tensorflow Serving.
 
 Btw, the image exists here: [epigramai/model-server:devel](https://hub.docker.com/r/epigramai/model-server/tags/).

#### Compile and commit
Run the docker image with `docker run --name NAME -it REPONAME (the remoname you chose above)`.

In the running docker container clone TensorFlow Serving code `git clone --recurse-submodules https://github.com/tensorflow/serving`.


Build what's needed to run the model server
- `cd serving/tensorflow`
- `./configure` (Just hit enter until finished, unless you have special needs)
- `cd ..`
- `bazel build -c opt //tensorflow_serving/model_servers:tensorflow_model_server` (Or `bazel build -c opt //tensorflow_serving/...` if you want to build all code. Not needed to serve models.)
- docker commit CONTAINER REPONAME

By doint a commit, we store the current container to an image. The final image can be used to serve tf models. This image can be found [on epigram docker hub](https://hub.docker.com/r/epigramai/model-server/) with the `base` tag. DO NOT, NEVER, EVER OVERWRITE THIS IMAGE! It takes forever to build!

Run the docker image by doing `docker run --name NAME -p HOSTPORT:CONTAINERPORT -v /HOST/MODELPATH>:/CONTAINER/MODELPATH epigramai/model-server:base serving/bazel-bin/tensorflow_serving/model_servers/tensorflow_model_server --port=CONTAINERPORT --model_name=MODELAME --model_base_path=/CONTAINER/MODELPATH`

#### model-server-build
Although we can easily use the image built and ran in the previous section, we can make the run process even easier.

#### Dockerfile.serve
If you want to be able to run you image by just doing `docker run --name NAME -p HOSTPORT:CONTAINERPORT -v /HOST/MODELPATH>:/CONTAINER/MODELPATH epigramai/model-server:serve --port=CONTAINERPORT --model_name=MODELNAME --model_base_path=/CONTAINER/MODELPATH`. Yep, without the looong path to the built model server.

Go ahead and `docker build -t REPONAME -f Dockerfile.serve .`. An already built image can be found [on epigram docker hub](https://hub.docker.com/r/epigramai/model-server/) with the `serve` tag.

There you go - a docker image you can use to serve your models without building the models into the image docker image :)



### Client to send requests
TODO...
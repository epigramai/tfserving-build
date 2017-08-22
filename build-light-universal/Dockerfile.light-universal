FROM ubuntu:16.04

RUN apt-get update && apt-get install -y curl libcurl3 libcurl3-dev \
    && echo "deb [arch=amd64] http://storage.googleapis.com/tensorflow-serving-apt stable tensorflow-model-server tensorflow-model-server-universal" | tee /etc/apt/sources.list.d/tensorflow-serving.list \
    && curl https://storage.googleapis.com/tensorflow-serving-apt/tensorflow-serving.release.pub.gpg | apt-key add - \
    && apt-get update && apt-get install tensorflow-model-server-universal

ENTRYPOINT ["tensorflow_model_server"]

CMD ["/bin/bash"]
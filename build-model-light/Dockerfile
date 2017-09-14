FROM epigramai/model-server:light

# Copying the models we want in this image into the build
COPY /models/ .

ENTRYPOINT ["tensorflow_model_server"]

CMD ["--port=9000", "--model_name=mnist", "--model_base_path=mnist_cnn"]

# docker build -t epigramai/model-server:mnist_cnn_1 .
# docker run --name NAME -p HOSTPORT:CONTAINERPORT epigramai/model-server:mnist_cnn_1
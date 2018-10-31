FROM tensorflow/tensorflow:1.11.0

# Install TensorFlow C library
RUN curl -L https://storage.googleapis.com/tensorflow/libtensorflow/libtensorflow-cpu-linux-x86_64-1.11.0.tar.gz | \
    tar -xz -C "/usr/local" && \
    ldconfig

# Hide some warnings
ENV TF_CPP_MIN_LOG_LEVEL 2

# Install Go (https://github.com/docker-library/golang/blob/221ee92559f2963c1fe55646d3516f5b8f4c91a4/1.9/stretch/Dockerfile)
RUN apt-get update && apt-get install -y --no-install-recommends \
    g++ \
    gcc \
    libc6-dev \
    make \
    pkg-config \
    git \
	&& rm -rf /var/lib/apt/lists/*

ENV GOLANG_VERSION 1.11.1

RUN set -eux; \
	goRelArch="linux-$(dpkg --print-architecture)" \
	url="https://golang.org/dl/go${GOLANG_VERSION}.${goRelArch}.tar.gz"; \
	curl -L -o go.tgz "$url" && tar -C /usr/local -xzf go.tgz && rm go.tgz; \
	PATH="/usr/local/go/bin:$PATH" go version

ENV GOPATH /go
ENV PATH $GOPATH/bin:/usr/local/go/bin:$PATH

RUN mkdir -p "$GOPATH/src" "$GOPATH/bin" && chmod -R 777 "$GOPATH"

# Download InceptionV3 model
RUN mkdir -p /model && \
  curl -L -o /model/inception.zip https://storage.googleapis.com/download.tensorflow.org/models/inception5h.zip && \
  unzip /model/inception.zip -d /model && \
  chmod -R 777 /model

# Set up project directory
WORKDIR "/go/src/github.com/tinrab/go-tensorflow-image-recognition"
COPY . .

# Install the app
RUN GO111MODULE=on go build -o /usr/bin/app .

# Run the app
CMD [ "app" ]

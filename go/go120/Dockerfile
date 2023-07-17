# Copyright 2022 Google LLC
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

FROM golang:1.20

# Install dependencies
RUN set -ex; \
    apt-get update -y; \
    apt-get install -y \
    python3 python3-pip python3-venv \
    unzip wget vim; \
    rm -rf /var/lib/apt/lists/*

# Install protoc
RUN wget https://github.com/protocolbuffers/protobuf/releases/download/v3.13.0/protoc-3.13.0-linux-x86_64.zip
RUN unzip protoc-3.13.0-linux-x86_64.zip
RUN mv bin/protoc /bin/protoc && which protoc

# Install gcloud SDK
RUN echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] http://packages.cloud.google.com/apt cloud-sdk main" | tee -a /etc/apt/sources.list.d/google-cloud-sdk.list && \
    curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key --keyring /usr/share/keyrings/cloud.google.gpg add - && apt-get update -y && \
    apt-get install google-cloud-sdk -y

# Install docker
RUN apt-get update && apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    lsb-release \
    software-properties-common && \
    curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add - && \
    add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/debian \
    $(lsb_release -cs) \
    stable" && \
    apt-get update && \
    apt-get install -y docker-ce docker-ce-cli containerd.io

# Install tools used in build
RUN go install honnef.co/go/tools/cmd/staticcheck@latest && \
    go install github.com/jstemmer/go-junit-report@latest && \
    go install golang.org/x/lint/golint@latest && \
    go install golang.org/x/tools/cmd/goimports@latest

## Install tools for generating/verifying hashes
ADD base-tooling-requirements.txt .
RUN pip install --require-hashes -r base-tooling-requirements.txt

## Install python tool required at runtime
ADD requirements.txt .
RUN pip install --require-hashes -r requirements.txt

WORKDIR $GOPATH
ARG NODE_VERSION=10
FROM node:$NODE_VERSION
ARG version=next
WORKDIR /home/theia

# Base level
RUN apt-get update && \
    apt-get install --fix-missing -y locales net-tools curl build-essential git sudo unzip && \
    locale-gen en_US.UTF-8  && \
    curl -L -O https://github.com/protocolbuffers/protobuf/releases/download/v3.11.4/protoc-3.11.4-linux-x86_64.zip && \
    unzip protoc-3.11.4-linux-x86_64.zip && \
    mv bin/protoc /usr/local/bin/protoc && \
	mv include/* /usr/local/include && \
    rm -rf protoc-3.11.4-linux-x86_64.zip bin include readme.txt && \
    echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] http://packages.cloud.google.com/apt cloud-sdk main" | tee -a /etc/apt/sources.list.d/google-cloud-sdk.list && \
    curl https://packages.cloud.google.com/apt/doc/apt-key.gpg > /tmp/googlekey.key && \
    apt-key --keyring /usr/share/keyrings/cloud.google.gpg add /tmp/googlekey.key && \
    apt-get update && \
    apt-get install -y google-cloud-sdk kubectl && \
    wget -q -O /tmp/libpng12.deb http://mirrors.kernel.org/ubuntu/pool/main/libp/libpng/libpng12-0_1.2.54-1ubuntu1_amd64.deb && \
    dpkg -i /tmp/libpng12.deb && \
    rm /tmp/libpng12.deb && \
    curl -O https://download.docker.com/linux/static/stable/x86_64/docker-19.03.8.tgz && \
    tar -zxvf docker-19.03.8.tgz && \
    mv docker/docker /usr/local/bin/docker && \
    rm -rf docker && \
	rm -rf docker-19.03.8.tgz && \
    apt-get autoremove -y && \
    apt-get clean

ADD ./bashrc /home/theia/.bashrc

# See : https://github.com/theia-ide/theia-apps/issues/34
RUN adduser --disabled-password --gecos '' theia && \
    chmod g+rw /home && \
    mkdir -p /home/project/work && \
    chown -R theia:theia /home/theia && \
    chown -R theia:theia /home/project && \
    mkdir -p /etc/sudoers.d && \
    echo "theia ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/nopasswd 
    
USER theia
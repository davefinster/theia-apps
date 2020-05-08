FROM davefinster/theia:base

ADD ./package.json ./package.json

RUN yarn --pure-lockfile && \
    NODE_OPTIONS="--max_old_space_size=4096" yarn theia build && \
    yarn theia download:plugins && \
    yarn --production && \
    yarn autoclean --init && \
    echo *.ts >> .yarnclean && \
    echo *.ts.map >> .yarnclean && \
    echo *.spec.* >> .yarnclean && \
    yarn autoclean --force && \
    yarn cache clean

# See : https://github.com/theia-ide/theia-apps/issues/34
RUN adduser --disabled-password --gecos '' theia && \
    chmod g+rw /home && \
    mkdir -p /home/project/work && \
    mkdir -p /home/go && \
    mkdir -p /home/go-tools && \
    chown -R theia:theia /home/theia && \
    chown -R theia:theia /home/project && \
    chown -R theia:theia /home/go && \
    chown -R theia:theia /home/go-tools && \
    mkdir -p /etc/sudoers.d && \
    echo "theia ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/nopasswd && \
    apt-get update && \
    apt-get install -y sudo && \
    apt-get autoremove -y && \
    apt-get clean

USER theia

ENV GO_VERSION=1.14 \
    GOOS=linux \
    GOARCH=amd64 \
    GOROOT=/home/go \
    GOPATH=/home/go-tools
ENV PATH=$GOPATH/bin:$GOROOT/bin:$PATH
# install Go and Theia default settings

ADD ./settings.json /home/theia/.theia/settings.json

RUN curl -fsSL https://storage.googleapis.com/golang/go$GO_VERSION.$GOOS-$GOARCH.tar.gz | tar -C /home -xzv && \
    sudo chown -R theia:theia /home/theia/.theia

# install VS Code Go tools: https://github.com/Microsoft/vscode-go/blob/058eccf17f1b0eebd607581591828531d768b98e/src/goInstallTools.ts#L19-L45
RUN go get -u github.com/stamblerre/gocode && \
	mv $GOPATH/bin/gocode $GOPATH/bin/gocode-gomod && \
	go get -u -v github.com/mdempsky/gocode && \
    go get -u -v github.com/uudashr/gopkgs/cmd/gopkgs && \
    go get -u -v github.com/ramya-rao-a/go-outline && \
    go get -u -v github.com/acroca/go-symbols && \
    go get -u -v golang.org/x/tools/cmd/guru && \
    go get -u -v golang.org/x/tools/cmd/gorename && \
    go get -u -v github.com/fatih/gomodifytags && \
    go get -u -v github.com/haya14busa/goplay/cmd/goplay && \
    go get -u -v github.com/josharian/impl && \
    go get -u -v github.com/tylerb/gotype-live && \
    go get -u -v github.com/rogpeppe/godef && \
    go get -u -v github.com/zmb3/gogetdoc && \
    go get -u -v golang.org/x/tools/cmd/goimports && \
    go get -u -v github.com/sqs/goreturns && \
    go get -u -v winterdrache.de/goformat/goformat && \
    go get -u -v golang.org/x/lint/golint && \
    go get -u -v github.com/cweill/gotests/... && \
    go get -u -v github.com/alecthomas/gometalinter && \
    go get -u -v honnef.co/go/tools/... && \
    GO111MODULE=on go get -u -v github.com/golangci/golangci-lint/cmd/golangci-lint && \
    go get -u -v github.com/mgechev/revive && \
    go get -u -v github.com/sourcegraph/go-langserver && \
    go get -u -v github.com/go-delve/delve/cmd/dlv && \
    go get -u -v github.com/davidrjenni/reftools/cmd/fillstruct && \
    go get -u -v github.com/godoctor/godoctor && \
    GO111MODULE=on go get -u -v golang.org/x/tools/gopls@latest && \
    sudo rm -rf $GOPATH/src && \
    sudo rm -rf $GOPATH/pkg

# configure Theia
ENV SHELL=/bin/bash \
    THEIA_DEFAULT_PLUGINS=local-dir:/home/theia/plugins  \
    # configure user Go path
    GOPATH=/home/project
ENV PATH=$PATH:$GOPATH/bin

EXPOSE 3000
ENTRYPOINT [ "node", "/home/theia/src-gen/backend/main.js", "/home/project/work", "--hostname=0.0.0.0" ]
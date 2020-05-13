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

ADD ./settings.json /home/theia/.theia/settings.json

RUN sudo chown -R theia:theia /home/theia/.theia

# configure Theia
ENV SHELL=/bin/bash \
    THEIA_DEFAULT_PLUGINS=local-dir:/home/theia/plugins  \
    # configure user Go path
    GOPATH=/home/project
ENV PATH=$PATH:$GOPATH/bin

EXPOSE 3000
ENTRYPOINT [ "node", "/home/theia/src-gen/backend/main.js", "/home/project/work", "--hostname=0.0.0.0" ]
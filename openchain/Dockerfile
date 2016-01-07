FROM microsoft/aspnet:1.0.0-rc1-update1-coreclr

RUN mkdir /openchain
COPY project.json /openchain/project.json

WORKDIR /openchain

RUN cd /openchain \
    && dnu restore

EXPOSE 8080
ENTRYPOINT ["dnx", "start", "--server.urls", "http://0.0.0.0:8080"]

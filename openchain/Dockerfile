FROM microsoft/dotnet:latest

RUN mkdir -p /tmp/openchain \
    && mkdir -p /openchain

COPY project.json /tmp/openchain/project.json
COPY Program.cs /tmp/openchain/Program.cs

RUN cd /tmp/openchain \
    && dotnet restore \
    && dotnet publish -o /openchain

WORKDIR /openchain

EXPOSE 8080
ENTRYPOINT ["dotnet", "openchain.dll"]

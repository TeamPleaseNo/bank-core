FROM ubuntu as copy
RUN apt-get update
RUN apt-get install -y git
WORKDIR /back
ADD "https://www.random.org/cgi-bin/randbyte?nbytes=10&format=h" skipcache
RUN git clone https://github.com/TeamPleaseNo/bank-backend.git

# https://hub.docker.com/_/microsoft-dotnet
FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build
WORKDIR /source
EXPOSE 8000

# copy csproj and restore as distinct layers
COPY --from=copy /back/bank-backend .
COPY --from=copy /back/bank-backend/Backend-Bank/*.csproj ./Backend-Bank/
COPY --from=copy /back/bank-backend/Database/*.csproj ./Database/
RUN dotnet restore 

# copy everything else and build app
COPY --from=copy /back/bank-backend/Backend-Bank/. ./Backend-Bank/
COPY --from=copy /back/bank-backend/Database/. ./Database/

WORKDIR /source/Backend-Bank
RUN dotnet build -c Release --no-restore
RUN dotnet publish -c Release -o /app --no-build

# final stage/image
FROM mcr.microsoft.com/dotnet/aspnet:7.0
FROM mcr.microsoft.com/vscode/devcontainers/dotnet
RUN dotnet tool install -g dotnet-ef
ENV PATH $PATH:/root/.dotnet/tools

WORKDIR /app
COPY --from=build /app ./
COPY --from=copy /back/bank-backend/Database/. ./Database/
ENTRYPOINT dotnet Backend-Bank.dll
#See https://aka.ms/containerfastmode to understand how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM alpine:3.12.0 AS base

#install libs
RUN apk update && apk add --no-cache libstdc++ libintl icu bash krb5 openrc busybox-initscripts curl ca-certificates && rm -rf /var/cache/apk/*

WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/core/sdk:3.1-buster AS build
WORKDIR /src
COPY ["Chatty.Api/Chatty.Api.csproj", "Chatty.Api/"]
RUN dotnet restore "Chatty.Api/Chatty.Api.csproj"
COPY . .
WORKDIR "/src/Chatty.Api"
RUN dotnet build "Chatty.Api.csproj" -c Release -o /app/build

FROM build AS publish
#RUN dotnet publish "Chatty.Api.csproj" -c Release -o /app/publish
RUN dotnet publish -v=m -c Release -r linux-musl-x64 --self-contained true -p:Version=1.0.0 -p:PublishSingleFile=true -p:PublishDir=/app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["sh", "-c", "./Chatty.Api && tail -f /dev/null"]
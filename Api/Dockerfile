#See https://aka.ms/containerfastmode to understand how Visual Studio uses this Dockerfile to build your images for faster debugging.


FROM mongo:3.4.14
ARG SERVER
ARG PORT
ARG DATABASE
ARG USERNAME
ARG PASSWORD
WORKDIR mongoscripts
COPY . .
RUN tr -d '\r' <run-mongoscripts.sh >run-mongoscripts.sh.tmp && mv -f run-mongoscripts.sh.tmp run-mongoscripts.sh
CMD sh run-mongoscripts.sh ${SERVER} ${PORT} ${DATABASE} ${USERNAME} ${PASSWORD}


FROM mcr.microsoft.com/dotnet/aspnet:5.0-buster-slim AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/sdk:5.0-buster-slim AS build
WORKDIR /src
COPY ["Api.csproj", ""]
RUN dotnet restore "./Api.csproj"
COPY . .
WORKDIR "/src/."
RUN dotnet build "Api.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "Api.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "Api.dll"]
#stage 1 --> create environment to run aspnet:8
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
USER $APP_UID
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

#stage 2 --> create build enviroment to run application
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["K8sWebApplicationTest/K8sWebApplicationTest.csproj", "K8sWebApplicationTest/"]
RUN dotnet restore "K8sWebApplicationTest/K8sWebApplicationTest.csproj"
COPY . .
WORKDIR "/src/K8sWebApplicationTest"
RUN dotnet build "K8sWebApplicationTest.csproj" -c $BUILD_CONFIGURATION -o /app/build

#stage 3 --> publish the app optimization deployment
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "K8sWebApplicationTest.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

#stage 4 --> copy only publish app to run application
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "K8sWebApplicationTest.dll"]

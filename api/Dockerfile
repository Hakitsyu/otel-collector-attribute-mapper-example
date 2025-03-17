FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

RUN apt-get update && apt-get install -y unzip curl

ARG OTEL_VERSION=1.11.0
ADD https://github.com/open-telemetry/opentelemetry-dotnet-instrumentation/releases/download/v${OTEL_VERSION}/otel-dotnet-auto-install.sh otel-dotnet-auto-install.sh
RUN OTEL_DOTNET_AUTO_HOME="/otel-dotnet-auto" sh otel-dotnet-auto-install.sh

USER $APP_UID

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["WeatherForecastApi.csproj", "./"]
RUN dotnet restore "WeatherForecastApi.csproj"
COPY . .
WORKDIR "/src/"
RUN dotnet build "WeatherForecastApi.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "WeatherForecastApi.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .

ENTRYPOINT ["dotnet", "WeatherForecastApi.dll"]

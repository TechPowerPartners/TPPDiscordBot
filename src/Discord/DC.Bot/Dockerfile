#See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/runtime:8.0 AS base
USER app
WORKDIR /app

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["src/Discord/DC.Bot/DC.Bot.csproj", "src/Discord/DC.Bot/"]
COPY ["src/Backend/Api.Contracts/Api.Contracts.csproj", "src/Backend/Api.Contracts/"]
COPY ["src/Backend/Domain/Domain.csproj", "src/Backend/Domain/"]
COPY ["src/Common/Domain.Shared/Domain.Shared.csproj", "src/Common/Domain.Shared/"]
COPY ["src/Common/Queue.Contracts/Queue.Contracts.csproj", "src/Common/Queue.Contracts/"]
RUN dotnet restore "./src/Discord/DC.Bot/DC.Bot.csproj"
COPY . .
WORKDIR "/src/src/Discord/DC.Bot"
RUN dotnet build "./DC.Bot.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./DC.Bot.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "DC.Bot.dll"]
# See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

# This stage is used when running from VS in fast mode (Default for Debug configuration)
FROM mcr.microsoft.com/dotnet/runtime:9.0 AS base
USER $APP_UID
WORKDIR /app

# Pour le managed identity
ENV AZURE_CLIENT_ID="722815e8-ef2b-4564-9179-f0ba95afd86e"
ENV AZURE_CLIENT_SECRET="lNR8Q~A2VdolzCe2dFdobHhc2hG4zotSStuFuaOf"
ENV AZURE_TENANT_ID="2b2a6365-d23a-4e3d-a0be-a44e8f5319b9"

# This stage is used to build the service project
FROM mcr.microsoft.com/dotnet/sdk:9.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["Worker_Image/Worker_Image.csproj", "Worker_Image/"]
RUN dotnet restore "./Worker_Image/Worker_Image.csproj"
COPY . .
WORKDIR "/src/Worker_Image"
RUN dotnet build "Worker_Image.csproj" -c $BUILD_CONFIGURATION -o /app/build

# This stage is used to publish the service project to be copied to the final stage
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "Worker_Image.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# This stage is used in production or when running from VS in regular mode (Default when not using the Debug configuration)
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .

ENTRYPOINT ["dotnet", "Worker_Image.dll"]
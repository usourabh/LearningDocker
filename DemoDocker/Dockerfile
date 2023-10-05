# Use the base image with the desired .NET SDK version for building
FROM mcr.microsoft.com/dotnet/sdk:3.1 AS build
WORKDIR /src
COPY ["DemoDocker/DemoDocker.csproj", "DemoDocker/"]
RUN dotnet restore "DemoDocker/DemoDocker.csproj"
COPY . .
WORKDIR "/src/DemoDocker"
RUN dotnet build "DemoDocker.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "DemoDocker.csproj" -c Release -o /app/publish

# Use the base image with ASP.NET Core runtime for running the application
FROM mcr.microsoft.com/dotnet/aspnet:3.1 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

# Install the .NET Core SDK within this stage
RUN apt-get update && apt-get install -y wget apt-transport-https && \
    wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb && \
    dpkg -i packages-microsoft-prod.deb && \
    apt-get update && \
    apt-get install -y dotnet-sdk-3.1

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "DemoDocker.dll"]

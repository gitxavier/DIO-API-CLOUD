# Fase base: imagem de runtime
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

# Fase de build: compilação da aplicação
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src

# Copiar apenas o arquivo de projeto para aproveitar cache de restore
COPY ["APITempoDIO.csproj", "./"]
RUN dotnet restore "APITempoDIO.csproj"

# Copiar o restante dos arquivos para a imagem
COPY . .

# Build da aplicação
RUN dotnet build "APITempoDIO.csproj" -c $BUILD_CONFIGURATION -o /app/build

# Fase de publicação: gerar os artefatos de publicação
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "APITempoDIO.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# Fase final: imagem de runtime com a aplicação publicada
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "APITempoDIO.dll"]

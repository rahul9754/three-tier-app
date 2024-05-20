
FROM mcr.microsoft.com/dotnet/sdk:7.0 as build

WORKDIR /source

COPY . .

RUN dotnet restore

RUN dotnet publish -c release -o /app

FROM mcr.microsoft.com/dotnet/runtime:7.0

WORKDIR /app

COPY --from=build /app .

ENTRYPOINT ["dotnet", "Worker.dll"]

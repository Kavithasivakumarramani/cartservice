# Use a base image with the necessary SDK
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 7070  

# Use the SDK image for building the app
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src

# Copy the necessary project files
COPY ["src/cartservice/cartservice.csproj", "src/cartservice/"]

# Copy the protos folder (ensure the path is correct)
COPY ["src/protos", "src/protos"]

# Restore the project dependencies
RUN dotnet restore "src/cartservice/cartservice.csproj" -r linux-musl-x64

# Copy the remaining source files
COPY . ./

# Publish the application
WORKDIR "/src/cartservice"
RUN dotnet publish "src/cartservice/cartservice.csproj" -p:PublishSingleFile=true -r linux-musl-x64 --self-contained true -p:PublishTrimmed=True -p:TrimMode=Full -c release -o /cartservice --no-restore

# Final image stage
FROM base AS final
WORKDIR /app
COPY --from=build /cartservice .
ENTRYPOINT ["./cartservice"]

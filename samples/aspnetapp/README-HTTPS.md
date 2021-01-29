# Hosting ASP.NET Core images with Docker over HTTPS

[For more details check the full documentation here](https://docs.microsoft.com/pt-br/aspnet/core/security/docker-https?view=aspnetcore-5.0)

ASP.NET Core uses HTTPS by default. HTTPS relies on certificates for trust, identity, and encryption.

This document explains how to run pre-built container images with HTTPS.

See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.

This sample requires Docker 17.06 or later of the Docker client.

[For more details check the full documentation here](https://docs.microsoft.com/pt-br/aspnet/core/security/docker-https?view=aspnetcore-5.0)

## Setting up the container using Powershell

Generate certificate and configure local machine:

```console
dotnet dev-certs https -ep $env:USERPROFILE\.aspnet\https\aspnetapp.pfx -p passwordhere
dotnet dev-certs https --trust
```

In the preceding commands, replace `passwordhere` with a password.

Configure application secrets, for the certificate:

```console
dotnet user-secrets -p aspnetapp\aspnetapp.csproj set "Kestrel:Certificates:Development:Password" "passwordhere"
```

In the preceding commands, replace `passwordhere` with a password.

Note: The password must match the password used for the certificate.

Build a container image:

```console
docker build --pull -t aspnetapp .
```

Run the container image with ASP.NET Core configured for HTTPS in a command shell:

```console
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_ENVIRONMENT=Development -v $env:APPDATA\microsoft\UserSecrets\:/root/.microsoft/usersecrets -v $env:USERPROFILE\.aspnet\https:/root/.aspnet/https/ aspnetapp
```

After the application starts, navigate to http://localhost:8000 in your web browser.

Removing certificates:

```console
dotnet dev-certs https --clean
```

Note: The certificate in this case must be a .pfx file. Utilizing a .crt or .key file with or without the password isn't supported with the sample container. For example, when specifying a .crt file, the container may return error messages such as 'The server mode SSL must use a certificate with the associated private key.'. When using WSL, validate the mount path to ensure that the certificate loads correctly.

## More Samples

* [.NET Docker Samples](../README.md)
* [.NET Framework Docker Samples](https://github.com/microsoft/dotnet-framework-docker/blob/master/samples/README.md)

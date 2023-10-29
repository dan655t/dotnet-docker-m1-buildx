# dotnet docker linux/amd64 buildx issue on M1

## dotnet 6 (works)

```
> docker buildx build --platform linux/amd64 -t dotnet6 -f Dockerfile.dotnet6 .
```

```
[+] Building 29.7s (13/13) FINISHED
 => [internal] load .dockerignore
 => => transferring context: 48B
 => [internal] load build definition from Dockerfile.dotnet6
 => => transferring dockerfile: 348B
 => [internal] load metadata for mcr.microsoft.com/dotnet/sdk:6.0
 => [internal] load metadata for mcr.microsoft.com/dotnet/runtime-deps:6.0
 => [build 1/4] FROM mcr.microsoft.com/dotnet/sdk:6.0@sha256:0f4f696537a786fbe9f3f98a0ceccccc054bd1a9b81b64aa06d6acf8ecdf2db5
 => [internal] load build context
 => => transferring context: 351.66kB
 => [runtime 1/3] FROM mcr.microsoft.com/dotnet/runtime-deps:6.0@sha256:f8921129b1490efcffaeedbd9e63c5d5bbe2f7ac79d917e488f3c709d1b94564
 => CACHED [build 2/4] WORKDIR /app
 => CACHED [runtime 2/3] WORKDIR /app
 => [build 3/4] COPY ./dotnet6 ./
 => [build 4/4] RUN dotnet publish --configuration Release --output ./out --runtime linux-x64 --self-contained
 => [runtime 3/3] COPY --from=build /app/out ./
 => exporting to image
 => => exporting layers
 => => writing image sha256:077d1f4d37b90f2e29f261cc3a67f61ee50ba80676c52b187d0a4f9ce848adb5
 => => naming to docker.io/library/dotnet6
```

## dotnet 7 (segmentation fault)

```
> docker build --platform linux/amd64 -t dotnet7 -f Dockerfile.dotnet7 .
```

```
[+] Building 7.6s (10/12)
 => [internal] load .dockerignore
 => => transferring context: 48B
 => [internal] load build definition from Dockerfile.dotnet7
 => => transferring dockerfile: 348B
 => [internal] load metadata for mcr.microsoft.com/dotnet/runtime-deps:7.0
 => [internal] load metadata for mcr.microsoft.com/dotnet/sdk:7.0
 => [build 1/4] FROM mcr.microsoft.com/dotnet/sdk:7.0@sha256:4d9f2a7141eadcaf9b096103ad407ce374a9bb06f831fe306e03ed63786b8d68
 => [runtime 1/3] FROM mcr.microsoft.com/dotnet/runtime-deps:7.0@sha256:f8921129b1490efcffaeedbd9e63c5d5bbe2f7ac79d917e488f3c709d1b94564
 => [internal] load build context
 => => transferring context: 11.64kB
 => CACHED [build 2/4] WORKDIR /app
 => CACHED [build 3/4] COPY ./dotnet7 ./
 => ERROR [build 4/4] RUN dotnet publish --configuration Release --output ./out --runtime linux-x64 --self-contained
------
 > [build 4/4] RUN dotnet publish --configuration Release --output ./out --runtime linux-x64 --self-contained:
3.568 MSBuild version 17.7.3+4fca21998 for .NET
7.133 qemu: uncaught target signal 11 (Segmentation fault) - core dumped
7.223 Segmentation fault
------
Dockerfile.dotnet7:4
--------------------
   2 |     WORKDIR /app
   3 |     COPY ./dotnet7 ./
   4 | >>> RUN dotnet publish --configuration Release --output ./out --runtime linux-x64 --self-contained
   5 |
   6 |     FROM mcr.microsoft.com/dotnet/runtime-deps:7.0 AS runtime
--------------------
ERROR: failed to solve: process "/bin/sh -c dotnet publish --configuration Release --output ./out --runtime linux-x64 --self-contained" did not complete successfully: exit code: 139
```

## dotnet 8 (never finishes)

```
> docker build --platform linux/amd64 -t dotnet8 -f Dockerfile.dotnet8 .
```

```
[+] Building 1255.9s (11/12)                                                                                                                docker:desktop-linux
 => [internal] load .dockerignore                                                                                                                           0.0s
 => => transferring context: 48B                                                                                                                            0.0s
 => [internal] load build definition from Dockerfile.dotnet8                                                                                                0.0s
 => => transferring dockerfile: 348B                                                                                                                        0.0s
 => [internal] load metadata for mcr.microsoft.com/dotnet/runtime-deps:8.0                                                                                  0.7s
 => [internal] load metadata for mcr.microsoft.com/dotnet/sdk:8.0                                                                                           0.1s
 => [build 1/4] FROM mcr.microsoft.com/dotnet/sdk:8.0@sha256:4181663c57b6c182503e7245fb1e83f99818b71eb3d0b2ce504575b6556871ce                               0.0s
 => [internal] load build context                                                                                                                           0.0s
 => => transferring context: 8.93kB                                                                                                                         0.0s
 => [runtime 1/3] FROM mcr.microsoft.com/dotnet/runtime-deps:8.0@sha256:1eff7e8d25ebc126fa9afd600b94ca1a1b37edb2063afae57783b7bf2989d928                    0.0s
 => => resolve mcr.microsoft.com/dotnet/runtime-deps:8.0@sha256:1eff7e8d25ebc126fa9afd600b94ca1a1b37edb2063afae57783b7bf2989d928                            0.0s
 => => sha256:1eff7e8d25ebc126fa9afd600b94ca1a1b37edb2063afae57783b7bf2989d928 1.08kB / 1.08kB                                                              0.0s
 => => sha256:6025941bcb330fdfb09af3ec864e9a354f6d83d4bcae0b487c5cbd6fd545e2af 949B / 949B                                                                  0.0s
 => => sha256:16e5ff122c82e6f709fbb15decbd8e3a4e6c87186e46993901db94c6a8c061c4 1.67kB / 1.67kB                                                              0.0s
 => CACHED [build 2/4] WORKDIR /app                                                                                                                         0.0s
 => [build 3/4] COPY ./dotnet8 ./                                                                                                                           0.0s
 => [runtime 2/3] WORKDIR /app                                                                                                                              0.0s
 => CANCELED [build 4/4] RUN dotnet publish --configuration Release --output ./out --runtime linux-x64 --self-contained                                  1255.1s
```

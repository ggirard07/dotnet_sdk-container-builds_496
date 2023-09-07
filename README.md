# Requirements

## Requirements
1. Run local repository for auth purposes
    ```pwsh
    docker run -d -p 5000:5000 --name registry registry:2
    ```
1. Build and publish base image with rootless user to local repository
   ```pwsh
   docker build --pull --rm -f ".\ImageKeepRoot\Dockerfile.mybaseimage" -t localhost:5000/mybaseimage:latest .
   docker push localhost:5000/mybaseimage:latest
   ```

## To reproduce issue
Publish the project then start resulting image with connecting to bash, which use default user configured in image

```pwsh
dotnet publish .\ImageKeepRoot\ImageKeepRoot.csproj --configuration Release --os linux --arch x64 --property:PublishProfile=DefaultContainer
docker run --rm -it --entrypoint /bin/bash imageshouldnotberoot-latest:1.0.0
```

### Expected

```pwsh
app@232a7e2edb85:/app$ id
uid=1654(app) gid=1654(app) groups=1654(app)
```

### Acutal

```pwsh
root@ca2887031384:/app# id
uid=0(root) gid=0(root) groups=0(root)
```

## For workaround

Uncomment line 13 in [ImageKeepRoot.csproj](./ImageKeepRoot/ImageKeepRoot.csproj) then try publish and run command above again.




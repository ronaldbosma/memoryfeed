# Docker

## List images

```
docker image ls
```

## List containers

```
docker ps
docker ps --all
```

## Show stopped containers

```
docker ps --filter "status=exited"
```

## Copy file

The following command copies a file from a container named 'mycontainer', to the host (current directory).

```
docker cp mycontainer:/folder/subfolder/file.zip file.zip
```
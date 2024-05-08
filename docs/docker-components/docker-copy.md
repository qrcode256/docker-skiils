### Docker command COPY
```sh
COPY ./src ./Makefile /src # will copy src and Makefile to src in image
```

```sh
COPY . /src # copy from current host folder to src in image folder
```
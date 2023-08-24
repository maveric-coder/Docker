# Dockerfile

### Difference between docker-compose and Dockerfile

The contents of a Dockerfile describe how to create and build a Docker image, while docker-compose is a command that runs Docker containers based on settings described in a docker-compose.yaml file.

Let's create a small script named *log-event.sh* which we will execute later.
```bash
#!/bin/sh

echo `date` $@ >> log.txt;
cat log.txt;
```

We will use the created script in the Dockerfile.
```Dockerfile
FROM alpine
ADD log-event.sh /
```
## RUN
The run instruction executes when we build the image. That means the command passed to run executes on top of the current image in a new layer. Then the result is committed to the image. Let's see how this looks in action.

Firstly, we'll add a run instruction to our Dockerfile:
```Dockerfile
FROM alpine
ADD log-event.sh /
RUN ["/log-event.sh", "image created"]
```

Secondly, let's build our image with:

```bash
docker build -t myimage .
```

Now we expect to have a Docker image containing a log.txt file with one image created line inside. Let's check this by running a container based on the image:

```bash
docker run myimage cat log.txt
```

When listing the contents of the file, we'll see an output like this:

`Thu Aug 24 18:14:56 UTC 2023 image created`

If we run the container several times, we'll see that the date in our log file doesn't change. This makes sense because the run step executes at image build time, not at the container runtime.

## CMD
With the **cmd** instruction, we can specify a default command that executes when the container is starting. Let's add a cmd entry to our Dockerfile and see how it works:
```Dockerfile
FROM alpine
ADD log-event.sh /
RUN ["/log-event.sh", "image created"]
CMD ["/log-event.sh", "container started"]
```
Let's build the image again with added command and the build a container to see the output.
```bash
$ docker run myimage
```
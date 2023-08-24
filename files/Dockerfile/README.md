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
`Thu Aug 24 18:14:56 UTC 2023 image created`
`Thu Aug 24 18:29:16 UTC 2023 container started`

If we run this multiple times, we'll see that the image created entry stays the same. But the container started entry updates with every run. This shows how cmd indeed executes every time the container starts.

Notice we've used a slightly different docker run command to start our container this time. Let's see what happens if we run the same command as before:
```bash
$ docker run myimage cat log.txt
```
`Thu Aug 24 18:14:56 UTC 2023 image created`
This time the cmd specified in the Dockerfile is ignored. That's because we have specified arguments to the docker run command.

Let's move on now and see what happens if we have more than one cmd entry in the Dockerfile. Let's add a new entry that will display another message:
```Dockerfile
FROM alpine
ADD log-event.sh /
RUN ["/log-event.sh", "image created"]
CMD ["/log-event.sh", "container started"]
CMD ["/log-event.sh", "container started"]
```
After building the image and running the container again, we'll find the following output:

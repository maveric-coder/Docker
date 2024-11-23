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
RUN chmod 700 log-event.sh
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
RUN chmod 700 log-event.sh
RUN ["/log-event.sh", "image created"]
CMD ["/log-event.sh", "container started"]
```
Let's build the image again with added command and the build a container to see the output.
```bash
 docker run myimage
```
`Thu Aug 24 18:14:56 UTC 2023 image created`
`Thu Aug 24 18:29:16 UTC 2023 container started`

If we run this multiple times, we'll see that the image created entry stays the same. But the container started entry updates with every run. This shows how cmd indeed executes every time the container starts.

Notice we've used a slightly different docker run command to start our container this time. Let's see what happens if we run the same command as before:
```bash
 docker run myimage cat log.txt
```
`Thu Aug 24 18:14:56 UTC 2023 image created`
This time the cmd specified in the Dockerfile is ignored. That's because we have specified arguments to the docker run command.

Let's move on now and see what happens if we have more than one cmd entry in the Dockerfile. Let's add a new entry that will display another message:
```Dockerfile
FROM alpine
ADD log-event.sh /
RUN chmod 700 log-event.sh
RUN ["/log-event.sh", "image created"]
CMD ["/log-event.sh", "container started1"]
CMD ["/log-event.sh", "container started2"]
```
After building the image and running the container again, we'll find the following output:
`Thu Aug 24 18:14:56 UTC 2023 image created`
`Thu Aug 24 18:31:14 UTC 2023 container started2`

As we can see, the container started entry is not present, only the container running is. That's because only the last cmd is invoked if more than one is specified.

## Entrypoint
As we saw above, cmd is ignored if passing any arguments when starting the container. What if we want more flexibility? Let's say we want to customize the appended text and pass it as an argument to the docker run command. For this purpose, let's use entrypoint. We'll specify the default command to run when the container starts. Moreover, we're now able to provide extra arguments.

Let's replace the cmd entry in our Dockerfile with entrypoint:
```Dockerfile
FROM alpine
ADD log-event.sh /
RUN chmod 700 log-event.sh
RUN ["/log-event.sh", "image created"]
ENTRYPOINT ["/log-event.sh"]
```
build the image and run the container by providing custom commmad
```bash
docker run myimage container running now
```
`Thu Aug 24 18:14:56 UTC 2023 image created`
`Thu Aug 24 18:43:22 UTC 2023 container running now`

We can see how entrypoint behaves similarly to cmd. And in addition, it allows us to customize the command executed at startup.

Like with cmd, in case of multiple entrypoint entries, only the last one is considered.

## Interactions Between cmd and entrypoint
We have used both cmd and entrypoint to define the command executed when running the container. Let's now move on and see how to use cmd and entrypoint in combination.

One such use-case is to define default arguments for entrypoint. Let's add a cmd entry after entrypoint in our Dockerfile:

```Dockerfile
FROM alpine
ADD log-event.sh /
RUN chmod 700 log-event.sh
RUN ["/log-event.sh", "image created"]
ENTRYPOINT ["/log-event.sh"]
CMD ["container started"]
```
Now, let's run our container without providing any arguments, and with the defaults specified in cmd:

```bash
docker run myimage
```
`Thu Aug 24 18:14:56 UTC 2023 image created`
`Thu Aug 24 18:52:48 UTC 2023 container started`

We can also override them if we choose so:
```bash
docker run myimage custom event
```
`Thu Aug 24 18:14:56 UTC 2023 image created`
`Thu Aug 24 18:53:18 UTC 2023 ccustom event`

## Differences Between ADD and COPY

## COPY instruction
The COPY instruction is straightforward and does exactly what its name implies: It copies files and directories from a source within the build context to a destination layer in the Docker image.
```Dockerfile
COPY <src>... <dest>
<src>: The source files or directories on the host.
<dest>: The destination path inside the Docker image.
```

* Basic functionality: COPY only supports copying files and directories from the host file system. It does not support URLs or automatic unpacking of compressed files.
* Security: Because COPY only handles local files, it tends to be more predictable and secure than ADD, reducing the risk of unintentionally introducing files from external sources.
* Use case: Best used when you need to include files from your local build context into the Docker image without any additional processing.


## ADD instruction
The ADD instruction provides the same functionality that the COPY instruction does, but it also has additional functionality that, if misunderstood, can introduce complexity and potential security risks.

```Dockerfile
ADD <src>... <dest>

ADD https://example.com/data.tar.gz /data
ADD ./src /app/src
<src>: The source files (directories or URLs).
<dest>: The destination path inside the Docker image.
```

* Extended functionality: In addition to copying local files and directories from the build context, ADD provides the following advanced functionality:
* Handle URLs: When supplied as a source, the file referenced by a URL will be downloaded to the current Docker image layer at the supplied destination path.
* Extract archives: When supplied as a source, ADD will automatically unpack and expand archives to the current Docker image layer at the supplied destination path.
* Flexibility vs. security: Although ADD is more flexible, it does introduce risk. Downloading external URLs into the build process may allow malicious code or contents to be brought into the process. Using ADD with archives may result in unintended consequences if you do not understand how it handles archives.
* Use case: ADD should only be used when you need specific functionality that it provides and are willing to manage the potential security issues arising from this usage.

### When to use COPY vs. ADD
For most use cases, COPY is the better choice due to its simplicity and security. This instruction allows you to transfer files and directories from your local context into the Docker image you are building.<br>
Use ADD only when you need the additional capabilities it offers, but be mindful of potential security implications.

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
Copy
Secondly, let's build our image with:

docker build -t myimage .
Copy
Now we expect to have a Docker image containing a log.txt file with one image created line inside. Let's check this by running a container based on the image:

docker run myimage cat log.txt
Copy
When listing the contents of the file, we'll see an output like this:

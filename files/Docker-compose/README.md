# Docker Compose
Compose uses YAML files to define multi-service applications. YAML is a subset of JSON, so you can also use JSON.
<br>The default name for the Compose YAML file is docker-compose.yml. However, you can use the -f flag to specify custom filenames.

```bash
version: "3.5"
services:
  web-fe:
    build: .
    command: python app.py
    ports:
      - target: 5000
        published: 5000
    networks:
      - counter-net

     volumes:
      - type: volume
        source: counter-vol
        target: /code
  redis:
    image: "redis:alpine"
    networks:
      counter-net:
networks:
  counter-net:
volumes:
  counter-vol:

```
The version key is mandatory, and it’s always the first line at the root of the file. This defines the version of the Compose file format (basically the API). It’s important to note that the versions key does not define the version of Docker Compose or the Docker Engine. 

The top-level services key is where we define the different application services. 


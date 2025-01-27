# Containerizing the API

We will take the microservice created in step10_microserice_db and containerize it.

## Let's First Run the app without Docker to test if everything is working

We have updated our toml file using this as a base:

https://github.com/tiangolo/full-stack-fastapi-template/blob/master/backend/pyproject.toml

Note: Review each and every dependency in the toml file. So that you learn what are the different libraries used in development.


Note: If you are using Kubernetes or similar tools. In that case, you probably don't need this image (or any other similar base image). You are probably better off building a Docker image from scratch as explained in the docs for FastAPI in Containers - [Docker: Build a Docker Image for FastAPI](https://fastapi.tiangolo.com/deployment/docker/#replication-number-of-processes).

Install dependency:

    poetry install

Run project in Poetry Envirnoment, to see if it is running outside a container:

    poetry run uvicorn app.main:app --host 0.0.0.0 --port 8000

Open in Browser:

    http://0.0.0.0:8000/

    http://0.0.0.0:8000/docs

    http://0.0.0.0:8000/openapi.json

Run test:

    poetry run pytest


## Now Let's Containerize the App

We are using this Dockerfile template:

https://github.com/tiangolo/full-stack-fastapi-template/blob/master/backend/Dockerfile

Now we will use the following Docker base image:

https://hub.docker.com/r/tiangolo/uvicorn-gunicorn-fastapi


**Checking to see if Docker is running:**

```bash
docker version
```

**Building the Image for Dev:**

```bash
docker build -f Dockerfile.dev -t my-dev-image .
```

**Check Images:**

```bash
docker images
```

**Verify the config:**

```bash
docker inspect my-dev-image
```

**Running the Container for Dev:**

https://docs.docker.com/engine/reference/run/


**Replace $(pwd) with your codebase's location.**

*i.e. docker run -d --name dev-cont1 -v D:\code\learn-generative-ai\05_microservices_all_in_one_platform\14_docker\02_containerizing_api:/code -p 8000:8000 my-dev-image*

```bash
docker run -d --name dev-cont1 -v $(pwd):/code -p 8000:8000 my-dev-image
```

**container logs**
```bash
docker logs dev-cont1
```

**container logs: To follow only new log files you can use -f --since 0m  --tail 10**
```bash
docker logs dev-cont1 -f
docker logs dev-cont1 -f --tail 10
docker logs dev-cont1 -f --since 10m
```

**Test the Container:**

```bash
docker run -it --rm my-dev-image /bin/bash -c "poetry run pytest"
```

**Intract with the Container:**

```bash
docker exec -it dev-cont1 /bin/bash
```

**Exit from the container shell**
```bash
exit
```

**List Running Containers**

```bash
docker ps
```

**List all Containers**

```bash
docker ps -a
```

## Production Setup for On-premises Environment

[For production deployments we recommend using gunicorn with the uvicorn worker class.](https://www.uvicorn.org/#running-with-gunicorn)

For a PyPy compatible configuration use uvicorn.workers.UvicornH11Worker.

For more information, see the [deployment documentation.](https://www.uvicorn.org/deployment/)


**Building the Image for Production:**

```bash
docker build -f Dockerfile.onprem_prod -t my-onprem-prod-image .
```

**Running the Container for Production:**

```bash
docker run -d -p 8080:8000 my-onprem-prod-image
```

## Building fully optimized production images for Kubernetes or serverless cloud environments:**

Docker Image with Poetry

If you use Poetry to manage your project's dependencies, you could use Docker multi-stage building:

https://fastapi.tiangolo.com/deployment/docker/

**Building fully optimized Image for Production:**

```bash
docker build -f Dockerfile.prod -t my-prod-image-optimized .
```

**Running the Container for Production:**

```bash
docker run -d -p 8000:8000 my-prod-image-optimized
```


Additional References:

https://www.jeffastor.com/blog/testing-fastapi-endpoints-with-docker-and-pytest/
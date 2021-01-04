### Build the image
```
$ docker build -t <name_tag> .
```

### Run the image

To run the image and bind to host port 8529:

```
$ docker run -d --name arangodb -p 8529:8529 <name_tag>
```

The first time you run your container,  a new user `arango` with all privileges will be created with a random password.
To get the password, check the logs of the container by running:

```
docker logs <CONTAINER_ID>
```

You will see an output like the following:

```
========================================================================
ArangoDB User: "arango"
ArangoDB Password: "WbvIcyqXey0XlZgI"
========================================================================
```

#### Credentials

If you want to preset credentials instead of a random generated ones, you can set the following environment variables:

* `ARANGODB_USERNAME` to set a specific username
* `ARANGODB_PASSWORD` to set a specific password

On this example we will preset our custom username and password:

```
$ docker run -d \
    --name arangodb \
    -p 8529:8529 \
    -e ARANGODB_USERNAME=myusername \
    -e ARANGODB_PASSWORD=mypassword \
    frodenas/arangodb
```

#### Databases

If you want to create a database at container's boot time, you can set the following environment variables:

* `ARANGODB_DBNAME` to create a database

On this example we will preset our custom username and password and we will create a database:

```
$ docker run -d \
    --name arangodb \
    -p 8529:8529 \
    -e ARANGODB_USERNAME=myusername \
    -e ARANGODB_PASSWORD=mypassword \
    -e ARANGODB_DBNAME=mydb \
    frodenas/arangodb
```

#### Persistent data

The ArangoDB server is configured to store data in the `/data` directory inside the container. You can map the
container's `/data` volume to a volume on the host so the data becomes independent of the running container:

```
$ mkdir -p /tmp/arangodb
$ docker run -d \
    --name arangodb \
    -p 8529:8529 \
    -v /tmp/arangodb:/data \
    frodenas/arangodb
```

There are also additional volumes at:

* `/var/log/arangodb` which exposes ArangoDB's log files
* `/etc/arangodb` which exposes ArangoDB's configuration
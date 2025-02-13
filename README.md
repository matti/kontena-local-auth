[![Build Status](https://travis-ci.org/jnummelin/kontena-local-auth.svg?branch=master)](https://travis-ci.org/jnummelin/kontena-local-auth)

# kontena-local-auth

This project provides local self-contained authentication backend for Kontena.

The main purpose is to allow organizations to setup fully private Kontena masters that do not need any access to external services.


# Architecture

The APi is implemented using Roda REST API framework. As a persistent storage all user information is stored in local sqlite DB.

The passwords are hashed using BCrypt algorithm which is pretty secure by nature and has salts etc. built in.

# Running the auth api

Naturally this should be run with Docker.

```
docker run -d -p 3000:3000 jnummelin/kontena-auth
```

## Running in prod

TODO:
- Describe volume setup for the DB file
- Describe easy SSL setup for HAProxy etc.

# Testing

Tests are implemented using rspec and ca be run by issuing:
```
rspec spec/
```

# Testing with Kontena master

For local testing with Kontena master there's ready made docker-compose.yml that sets up Kontena master and Mongo for it. It also sets the master to use this local auth container as its auth backend.

# Integrating with Kontena master

To setup your own Kontena master to use this auth API as backend use following env variable for the master:
```
AUTH_API_URL=http://authapi.somehost.io:5000
```

# Backing up

Make sure you take backups of your user data. The auth service uses Sqlite database to store data. By default the user database is store at `/data/users.db`. The path `/data` is declared as volume so docker will store it outside of the container layer. In order to take backups you can mount that same volume in another container and send the data somewhere safe. Something like this:
```
docker run --rm --volumes-from kontena-local-auth ubuntu ./send_to_s3.sh /data/users.db
```

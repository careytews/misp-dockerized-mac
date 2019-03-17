# Run MISP-dockerized on macos

**NOTE:** Thes instructions are current as of [DCSO/MISP-dockerized](https://github.com/DCSO/MISP-dockerized) v 1.0.3.

## Introduction
If you are trying to deploy `MISP-dockerized` on macos, you're out of luck. There is no `docker` group on macos, and, since Darwin is based on BSD, built-in commands have different arguments, etc.

I wanted to get MISP up and running on macos for testing at home, so I made use of the `docker:dind` image. In a world where the we are told "Don't run Docker in Docker, because reasons!", this is a perfect application.

Best of all, it works.

Here's how.

## From the Mac

```shell
# Run Docker in Docker
docker run --privileged --name dind -d docker:dind 

# Exec into the container, which is based on Alpine
docker exec -it dind /bin/ash
```

## In the container

```shell
# Add build dependencies
apk update && apk add git make bash

# Clone DCSO/MISP-dockerized
git clone https://github.com/dcso/misp-dockerized.git

# Change to the new directory
cd misp-dockerized

# Run make. I used 127.0.0.1 as the FQDN, and 
# accepted the defaults for everything else.
make install

# Wait for the install to finish and exit 
exit 
```

## Back on the Mac

```shell
# Copy the whole directory to your Mac
docker cp dind:misp-dockerized misp-dockerized-new

# We're done with dind
docker rm -f dind
```

## Et voilà

```shell
# Change to the location of the installation (current)
cd /path/to/misp-containerized/new/current

# Bring it up
make deploy
```

Point your favourite browser at https://127.0.0.1/users/login, and you should get the login page.

Use the standard admin username and password to login!
# API for Vigilante Web Heist

## Installing postgres

This is geared towards Mac users, sorry in advance for any non-Mac
environments. Anyways, this documentation is based on [these amazing docs](http://postgresguide.com/setup/install.html) for installing and using Postgres, and the link covers Mac, Linux, and Window installations.

1. Install Postgres locally by following [these
   instructions](http://postgresguide.com/setup/install.html). (If using a Mac
   you will download the Postgres app)

2. Start up Postgres on port 5432 (if you have an error because there is already
   a process running on this port, you likely already have a previous version of
   postgres running. The following command should fix it: `sudo pkill -u postgres`).

3. Run `psql` to start a command-line client for interacting with Postgres (if
   you are having a `command not found` error and have a Mac, run the following
   command to link the command line tools with Postgres: `sudo mkdir -p /etc/paths.d && echo /Applications/Postgres.app/Contents/Versions/latest/bin | sudo tee /etc/paths.d/postgresapp`).

4. Now that you are in the psql client, run the following commands to create a
   `gorm` database along with a `gorm` user (based on [this
   documentation](http://postgresguide.com/setup/users.html)).

   1. `CREATE USER gorm WITH PASSWORD 'gorm';`
   2. `CREATE DATABASE gorm;`
   3. `GRANT ALL PRIVILEGES ON DATABASE gorm to gorm;`

5. Now you should be able to build and run the API without any issues.
   At this point, I would suggest installing a Postgres GUI Client for
   interacting with the gorm database, such as
   [Postico](https://eggerapps.at/postico/) for Mac. Here is the [official
   list](https://postgresapp.com/documentation/gui-tools.html)
   of clients

## How to run

Prerequisites:

- Golang is installed
- Docker is installed
- Postgres is installed (should come with psql too)
- Postgres client is installed (optional but recommended)

#### Steps to run:

**With Golang**

1. Run `go build` at the root directory to compile the executable from the file "main.go"
2. Run the executable named "api" (e.g. if using mac run `./api`)

**With Docker**

Both a `Dockerfile` and a `Dockerfile.prod` are present for this service. The `Dockerfile` file is used to run the service locally, and the `Dockerfile.prod` is for a production environment, which needs a proxy alongside the api. To run just this image locally, use the following commands:

```bash
 docker build --tag api .      # Build the docker image
 docker run -p 8080:8080 api   # Run image as container on port 8080
```

Alternatively, You can set up the entire system using Docker and Docker-Compose. See more information and instructions in the [csci4950tgt/utils](https://github.com/csci4950tgt/utils) repository.

#### How to submit to Google Cloud Run:

While the owners of this repository figure out how they want to integrate CI/CD with this service, the only current way to deploy the api as a Cloud Run Service is through the following commands. Note that this will only work if you have the correct permissions with Google Cloud, and the required `secret.json` file.

```bash
 docker build . -f Dockerfile.prod --tag gcr.io/vigilante-web-heist/api  # Build image with Google's Container Registry
 gcloud run deploy --image gcr.io/vigilante-web-heist/api --platform managed  # Deploy as Cloud Run Service
```

#### Steps to test:

1. Setup a testing user and a testing database using `psql`
   1. `CREATE USER testing WITH PASSWORD 'testing';`
   2. `CREATE DATABASE testing;`
   3. `GRANT ALL PRIVILEGES ON DATABASE testing to testing;`
2. Run `go test ./...` to test recursively (or `go test ./... -v` if you want verbose information)

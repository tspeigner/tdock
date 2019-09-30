# About
This repo is my work for a technical task posed in an interview process.

# Getting Started
- _Note:_ This has been tested on MacOS, Linux
- [Docker](https://docs.docker.com/v17.12/install/)
- [Docker Compose](https://docs.docker.com/compose/install/)
- Ability to resolve example.test
  - With your favorite file editor, edit localhost file 0.0.0.0  example.test www.example.test
- Ports used: 
  - proxy => 80,443
  - web => 8443
- clone this repo to your machine `git clone https://github.com/tspeigner/tdock.git`
- Perform [Tests](#tests)

# Code comments
- Dockerfiles are pretty basic, but point at the public nginx:alpine image.
- docker-compose files ties the two containers together, mounts a number of files and folders
- Was not sure if the URL needed to be a randomly generated token or the static uj47G, but I believe with a bit of logic in the conf file there is a way to create a randomly generated token for the path.
- 

# Tech Task Requirements
- Deliverables
  - [Dockerfile for proxy](./proxy/Dockerfile)
  - [Dockerfile for web](./web/Dockerfile)
  - [docker-compose file](./docker-compose.yaml)
  - This document

# Tests
- docker build proxy 
- docker build web
- docker-compose up -d

# Flow: 
 - Request to http://example.test => https://example.test => https://web => https://web/uj47G
 - Resulting in response to browser https://example.test/uj47G or https://example.test/uj47G/index.html
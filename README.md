# SecureAPI Dev environment


## How to use
1. Clone all repos using `git clone git@github.com:hidalgopl/secureapi-dev.git --recurse-submodules`
2. Ensure you've got all submodules. Your directory tree should look like this:
```bash
.
├── docker-compose.yml
├── nats_config.conf
├── README.md
├── secureapi-boatswain
├── secureapi_web
└── test_saver
```
3. `docker-compose up` and you should be all set.

## Components
### NATS
Message queue to decouple services and make communication asynchronous.
[NATS](https://docs.nats.io) docs.
### PostgreSQL
DB to persist user's data & tests.
### SecureAPI-Boatswain
Component which listens for new messages on NATS (which are published by [sailor](https://github.com/hidalgopl/sailor) - our CLI tool. If he receives one, it runs security checks for given URL and publish results back to NATS queue.
### SecureAPI-Web
REST API for web interface. Written in django.
Swagger available on http://localhost:8072/swagger
### Test-saver
Component which listens for _complete_ messages in NATS, then takes result from message and saves it to DB.


## Generating test data
1. Log in using Github
2. Go to "User profile" tab and copy access_key
3. Create following `secureapi.yml` file:
```yaml
username: "<your-username>" # should be the same as on github
accessKey: "<your-accesskey>"
url: "https://api.facebook.com" # URL you want to test
natsUrl: "nats://localhost:4222" # this is address for NATS started by docker-compose
```
4. Run backends `docker-compose up`
5. Download latest [sailor CLI](https://github.com/hidalgopl/sailor) release, and run binary to execute tests: `sailor run --config=secureapi.yml`
6. Test should appear on top of Tests tab (latest are on top)

### Debugging:
1. Check if docker containers are up.
2. `docker-compose logs boatswain` -> check if tests were executed
2. `docker-compose logs test_saver` -> check if tests were saved to DB

---
sticker: 1f310
---
## <span style='color:#3867d6'>Redis</span> <mark style="background: #ABF7F7A6;">Oficial Redis image</mark>
```yaml
redis:
	image: "redis:7.0.4"
	init: true
	networks:
	- inside
	- test
	volumes:
	- ".docker/redis/redis.conf:/usr/local/etc/redis/redis.conf"
	- "./log/redis:/var/log/whitebox"
	healthcheck:
		test: [ "CMD", "redis-cli", "ping" ]
		interval: 10s
		timeout: 5s
		retries: 3
```

`init: true` -->
![[Pasted image 20230605165306.png]]
**init:true** helps ensure that the container shuts down more cleanly and safely by properly handling resource release and signal forwarding.

It has 2 networks avaliable:
- inside
- test

There are two volumes defined:

```yaml
".docker/redis/redis.conf:/usr/local/etc/redis/redis.conf"
```
Maps the local redis configuration file to the container.

```yaml
"./log/redis:/var/log/whitebox"
```
Log purposses

`healthcheck` --> As you may guessed, healthcheck purposes
- `test [ "CMD", "redis-cli", "ping" ]` --> command to run to check the health of the container. In this case, `redis-cli ping`. If this command returns an error, the container is considered unhealthy
- `interval: 10s` -->  This line indicates that Docker should run the health test every 10 seconds.
- `timeout: 5s` --> If the health test does not complete within 5 seconds, Docker considers it failed.
- `retries: 3` --> This line tells Docker to retry the health test 3 times before considering the container unhealthy.


## <span style='color:#3867d6'>Database</span> <mark style='background:#ABF7F7A6'>oficial PostgreSQL in Alpine</mark>

```yaml
database:
	image: "postgres:11.7-alpine"
	environment:
	- POSTGRES_HOST_AUTH_METHOD=trust
	init: true
	networks:
		- inside
		- test
	ports:
	- 5432:5432
	healthcheck:
		test: [ "CMD-SHELL", "pg_isready -U postgres" ]
		interval: 10s
		timeout: 5s
		retries: 3
```

`- POSTGRES_HOST_AUTH_METHOD=trust` --> means that no password will be required to connect to the PostgreSQL database.

![[Pasted image 20230605170717.png]]
https://www.postgresql.org/docs/current/auth-trust.htm

It has 2 networks avaliable:
- inside
- test


## <span style='color:#3867d6'>Image-service</span> <mark style='background:#ABF7F7A6'>image-service (ghcr.io)</mark>

```yaml
image-service:
	image: "ghcr.io/whiteboxservices/image-service:1.0.0"
	init: true
	networks:
		- inside
	volumes:
	- "./log/image-service:/opt/whitebox/image-service/log/:delegated"
	environment:
	# If NewRelic is enabled without valid license key, 
	# it crashes the services.
		- NEW_RELIC_ENABLED=false
```

It has 1 networks avaliable:
- inside

Volumes:
```yaml
"./log/image-service:/opt/whitebox/image-service/log/:delegated"
```
Log Purposes.
This volume has the <span style='color:#20bf6b'>":delegated"</span> option which, aparently is used to improve volume write performance from the host. But, I found that these kind of flags or options are redundant in lastest Docker versions. Take a look at this discussion:
https://github.com/docker/for-mac/issues/5402

Environment variables:
`- NEW_RELIC_ENABLED=false` --> This option disables the NewRelic service (New Relic is a metric service https://newrelic.com). 
It is disabled (see the coment)


## <span style='color:#3867d6'>Proxy-service</span> <mark style='background:#ABF7F7A6'> Squid:Ubuntu</mark> 🦑

```yml
proxy-service:
	image: ubuntu/squid:5.2-22.04_beta
	networks:
		- inside
	healthcheck:
		test: ["CMD-SHELL", "sh", "-exc", "service squid status | grep 'service squid status'" ]
		interval: 10s
		timeout: 5s
		retries: 5
		start_period: 5s
```

It has 1 networks avaliable:
- inside

`healthcheck` command --> 
```bash
sh -exc "service squid status | grep 'service squid status'"
```
Using grep, we are looking for the string "service squid status" in the output. If this command returns an error, the container is considered unhealthy.

[Wikipedia](https://es.wikipedia.org/wiki/Squid_(programa))
[Squid Oficial Website](http://www.squid-cache.org/)


## <span style='color:#3867d6'>Performance-service</span> <mark style='background:#ABF7F7A6'>performance-service(ghcr.io)</mark>

```yaml
performance-service:
	image: ghcr.io/whiteboxservices/performance-service:1.0.1
	init: true
	depends_on:
		database:
			condition: service_healthy
	networks:
	- inside
	environment:
	# Database URL
	- DB_NAME=whitebox_development
	- DB_HOST=database
	- DB_PORT=5432
	- DB_USER=postgres
	- DB_PASS=whoknows
	# DWH database URL
	- DWH_NAME=whitebox_dwh_development
	- DWH_HOST=database
	- DWH_PORT=5432
	- DWH_USER=postgres
	- DWH_PASS=whoknows
	- DWH_CACHE_UPDATE_WORKER_COUNT=10
	- MORNINGSTAR_SECURITY_DETAILS_ENDPOINT=https://euiwtqaext.morningstar.com
	- AIRBRAKE_PROJECT_KEY=11a870ead40e92417894a550a763d138
	- AIRBRAKE_PROJECT_ID=13231
	- PORT=8081
	healthcheck:
		test: [ "CMD-SHELL", "curl -XGET localhost:8081/health | grep true" ]
		interval: 10s
		timeout: 5s
		retries: 5
```

`depends_on:` --> 
depends on the `database` service. 
With the `condition: service_healthy` option, it indicates that `performance-service` should start only when the `database` service is healthy (passing the health check).

It has 1 networks avaliable:
- inside

Environment variables:
- MORNINGSTAR --> Morningstar, Inc. is an American financial services company.
- AIRBRAKE --> Error & Performance Monitoring


## <span style='color:#3867d6'>Projection-engine</span> <mark style='background:#ABF7F7A6'>projection-engine (ghcr.io)</mark>

```yaml
projection-engine:
	image: ghcr.io/whiteboxservices/projection-engine:1.0.0
	init: true
	networks:
		- inside
	environment:
		## New Relic
		- NEW_RELIC_LICENSE_KEY=***
		- NEW_RELIC_APP_NAME=Whitebox Projection-Engine (local)
		- NEW_RELIC_MONITOR_MODE=false # Turn on/off NewRelic monitoring
		- PROXY_HOST=proxy-service
		- PROXY_PORT=3128
	ports:
		- 8080:8080
	depends_on:
		proxy-service:
			condition: service_healthy
```

`depends on` the proxy-service.Projection-engine should start only when the proxy-service is healthy (passing the health check).

It has 1 networks avaliable:
- inside


## <span style='color:#3867d6'>Mailhog</span>  <mark style='background:#ABF7F7A6'>mailghog (ghcr.io)</mark>

```yml
mailhog:
	image: ghcr.io/whiteboxservices/mailhog:1.0.0
	networks:
		- inside
		- test
	# Mailhog introduces a user `mailhog` in their Dockerfile. To work around some
	# issues, e.g., https://github.com/docker/compose/issues/3270, and to save some
	# time we just run it under `root` like the rest of our services.
	user: root
	# entrypoint: /bin/sh -c "./run_mailhog.sh"
	volumes:
		- "./log/mailhog:/home/mailhog"
	init: true
	ports:
		- 8025:8025
	healthcheck:
		test: ["CMD-SHELL", "ps -ef | grep /usr/local/bin/MailHog"]
		interval: 10s
		timeout: 10s
		retries: 5
```

MailHog is an **email testing tool** that captures all sent emails and displays them in a web user interface. 
This is useful for development testing, as it allows to see all the mails that would be sent by the application without having to configure an SMTP mail server.


##  <span style='color:#3867d6'>Webpack</span>  <mark style="background: #ABF7F7A6;">via Dockerfile</mark>

```yml
webpack:
	build:
	context: .
	dockerfile: ./Dockerfile
	target: webpacker
	image: "whitebox-core-dev/webpack"
	volumes:
		- "./public/assets:/opt/whitebox/public/assets"
		- "./app/assets:/opt/whitebox/app/assets"
		- "./frontend:/opt/whitebox/frontend"
		- "./lib/assets:/opt/whitebox/lib/assets"
		 "./vendor/assets:/opt/whitebox/vendor/assets"
		- "./webpack.config.js:/opt/whitebox/webpack.config.js"
		- "./yarn.lock:/opt/whitebox/yarn.lock"
		- "./package.json:/opt/whitebox/package.json"
		- "./config:/opt/whitebox/config"
		- "./.babelrc:/opt/whitebox/.babelrc"
		- "./tsconfig.json:/opt/whitebox/tsconfig.json"
		- "./jestSetup.js:/opt/whitebox/jestSetup.js"
		- "./.prettierrc.yml:/opt/whitebox/.prettierrc.yml"
		- "./.prettierignore:/opt/whitebox/.prettierignore"
		- "./declarations.d.ts:/opt/whitebox/declarations.d.ts"
	init: true
	command: "yarn run watch"
	healthcheck:
		test: ["CMD-SHELL", "sh", "-exc", "ps -ef | grep webpack"]
		interval: 10s
		timeout: 5s
		start_period: 5s
		retries: 3
```

`build` -->
The build context (the folder that is copied to the Docker daemon during the build) is the current directory (.). 
The path to the Dockerfile is ./Dockerfile and the build `target` (the stage to be built) is webpacker.

`image` <span style='color:#20bf6b'>"whitebox-core-dev/webpack"</span> --> 
This is the label of the Docker image that will be created when this service is built.


## <span style='color:#3867d6'>Web</span>  <mark style="background: #ABF7F7A6;">via Dockerfile</mark>

```yaml
web:
	build:
	context: .
	dockerfile: Dockerfile
	target: dev
	image: whitebox-dev/web
	init: true
	depends_on:
		database:
			condition: service_healthy
		redis:
			condition: service_healthy
		image-service:
			condition: service_healthy
		performance-service:
			condition: service_healthy
		projection-engine:
			condition: service_healthy
		fake-idnow-server:
			condition: service_healthy
		fake-twilio-server:
			condition: service_healthy
		fake-address-validation-server:
			condition: service_healthy
		mailhog:
			condition: service_healthy
		worker:
			condition: service_healthy
		webpack:
			condition: service_healthy
	networks:
		- inside
	ports:
		- "5000:5000"
	environment: &environment
		#####################################
		## A LOT OF ENVIRONMENT VARIABLES   #
		## FOR ALL THE SERVICE DEPENDENCIES #
		#####################################
	stdin_open: true
	tty: true
	command: 'bash -c "rm -f tmp/pids/server.pid && bundle exec rails s -p 5000 -b ''0.0.0.0''"'
	healthcheck:
		test: ["CMD", "curl", "-f", "http://localhost:5000/healthcheck"]
		interval: 20s
		timeout: 3s
		start_period: 5s
		retries: 5
```

As you can see, **several services are built based on the same dockerfile**, this is possible thanks to the **multi-stage Docker build**. 

**Multi-stage Dockerfiles** allow you to use multiple FROM statements in the same Dockerfile. Each FROM can use a different image base and each one starts a new build stage.

The **target** is the name of the stage you want to build to. For example, in the webpack service, the target is **webpacker** and in the web service, the target is **dev**. Docker will build the Dockerfile up to that stage and not continue on. So even though you are using the same Dockerfile for both services, you are building **different stages for each**.

`build` --> 
Defines how the Docker image for this service is built.

`target: dev` -->
Will build the image to the phase named 'dev' defined in the Dockerfile.

`image: whitebox-dev/web` -->
Is the name and tag of the image to be built or used.


## <span style='color:#3867d6'>Worker</span>  <mark style="background: #ABF7F7A6;">via Dockerfile</mark>

```yml
worker:
	build:
	context: .
	dockerfile: Dockerfile
	target: dev
	image: "whitebox-core-dev/web"
	init: true
	depends_on:
		database:
			condition: service_healthy
		redis:
			condition: service_healthy
		image-service:
			condition: service_healthy
		performance-service:
			condition: service_healthy
		projection-engine:
			ondition: service_healthy
		mailhog:
			condition: service_started
		proxy-service:
			condition: service_healthy
		networks:
			- inside

	environment: *environment
	command: "bash -c 'SIDEKIQ_ENT_USERNAME=$$SIDEKIQ_ENT_USERNAME SIDEKIQ_CONCURRENCY=$$WORKER_CONCURRENCY bundle exec sidekiq --config ./config/sidekiq/default.yml & SIDEKIQ_ENT_USERNAME=$$SIDEKIQ_ENT_USERNAME SIDEKIQ_CONCURRENCY=$$PLACE_ORDER_CONCURRENCY bundle exec sidekiq --config ./config/sidekiq/order_requests.yml'"

	healthcheck:
		test: [ "CMD-SHELL", "ps aux | grep '[s]idekiq 6'" ]
		interval: 10s
		timeout: 5s
		retries: 5
```

There are **two diferent sidekiq instances** with two different configurations (default.yml and order_request.yml)


## <span style='color:#3867d6'>Migration-service</span>  <mark style="background: #ABF7F7A6;">via Dockerfile</mark>

```yaml
migration-service:
	build:
	context: .
	dockerfile: Dockerfile
	target: bundled
	image: "whitebox-core-dev/migration"
	profiles:
		- "db"
	init: true
	depends_on:
		database:
			condition: service_healthy
		redis:
			condition: service_healthy
		volumes:
			- .:/opt/whitebox:delegated
	networks:
		- inside
	environment: *environment
	command: /bin/true
```

A service to make the database migration proceses


## <span style='color:#3867d6'>Nginx</span> <mark style="background: #ABF7F7A6;">whitebox-nginx (ghcr.io)</mark>

```yml
nginx:
	image: ghcr.io/whiteboxservices/whitebox-nginx:1.0.0
	environment:
		- BACKEND_PROXY_PASS=http://web:5000
		- FRONTEND_PROXY_PASS=http://frontend:4000
	volumes:
		- ./log/nginx:/var/log/whitebox
	networks:
		- inside
	depends_on:
		web:
			condition: service_healthy
		frontend:
			condition: service_healthy
	ports:
		- 3000:3000
```

An instance of a Nginx server that act like a reverse proxy(? I'm not sure, but it seems) 


## <span style='color:#3867d6'>Nginx-e2e-test</span> <mark style="background: #ABF7F7A6;">whitebox-nginx (ghcr.io)</mark>

```yml
nginx-e2e-test:
	image: ghcr.io/whiteboxservices/whitebox-nginx:1.0.0
	environment:
		- BACKEND_PROXY_PASS=http://web:5000
		- FRONTEND_PROXY_PASS=http://frontend:4000
	volumes:
		- ./log/nginx:/var/log/whitebox
	depends_on:
		web-test:
			condition: service_healthy
		frontend-test:
			condition: service_healthy
	networks:
	test:
		aliases:
			- nginx
			- inside.whitebox.local
```

An instance of a Nginx server for **end to end testing** purposes.

## <span style='color:#3867d6'>Frontend</span>  <mark style="background: #ABF7F7A6;">via Dockerfile</mark>

```yml
frontend:
	build:
	context: frontend
	dockerfile: Dockerfile
	target: dev
	networks:
		- inside
	command: "yarn run dev -p 4000"
	environment:
		- API_HOST_FOR_BACKEND=http://web:5000
		- API_HOST_FOR_FRONTEND=http://inside.whitebox.local:3000
		- NEXT_PUBLIC_AIRBRAKE_PROJECT_KEY_JS=abcd
		- NEXT_PUBLIC_AIRBRAKE_PROJECT_ID_JS=13231
		- CACHE_ASSET_PATHS=false
		- ENV=development
	volumes:
		- ./frontend:/app
	ports:
		- 4000:4000
	healthcheck:
		test: ["CMD", "curl", "-f", "http://localhost:4000/assets/full_black.svg"]
		interval: 10s
		timeout: 5s
		start_period: 10s
		retries: 5
```

`volumes` --> 
Here the frontend directory of the host is mounted in the /app directory of the container. 
Changes made to the frontend directory on your host machine will be reflected in the container.

This service is the frontend application in a development environment. 
It connects to the network inside in order to communicate with other services.


## Cloned services for testing purposes
* Frontend-test
* Web-test
* End2end-test


## <span style='color:#3867d6'>Test</span>  <mark style="background: #ABF7F7A6;">via Dockerfile</mark>

```yaml
test:
	build:
	context: .
	dockerfile: ./Dockerfile
	target: ci
	container_name: test
	image: "whitebox-core-dev/test"
	init: true
	depends_on:
		database:
			condition: service_healthy
		redis:
			condition: service_healthy
		mailhog:
			condition: service_healthy
		chrome:
			condition: service_healthy
		fake-bank-soap-server:
			condition: service_healthy
		fake-iban-validation-server:
			condition: service_healthy
		fake-idnow-server-test:
			condition: service_healthy
		fake-address-validation-server:
			condition: service_healthy
		fake-sftp-file-server:
			condition: service_started
		fake-twilio-server-test:
			condition: service_healthy
		performance-service:
			condition: service_started
	networks:
		- test
	environment:
		#####################################
		## A LOT OF ENVIRONMENT VARIABLES   #
		## FOR ALL THE SERVICE DEPENDENCIES #
		#####################################
	tty: true
	stdin_open: true
```

`tty: true` ,  `stdin_open: true` --> 
Are set to allow the service to be interacted with via the terminal.


## <span style='color:#3867d6'>Chrome</span>  <mark style="background: #ABF7F7A6;">selenium/standalone-chrome-debug</mark>

```yaml
chrome:
	image: selenium/standalone-chrome-debug # Use this for x86_64
	# image: seleniarm/standalone-chromium:111.0 # Use this for M1, M2 or ARM64
	shm_size: 2g
	networks:
		- test
	environment:
		- VNC_NO_PASSWORD=1
	healthcheck:
		test: ["CMD", "curl", "-f", "http://localhost:4444"]
		interval: 10s
		timeout: 5s
		retries: 5
```

`shm_size: 2g` --> shared memory to this service, which can be useful for Chrome as it might require more shared memory than the default allocated by Docker.

the **VNC** server is probably included in the `selenium/standalone-chrome-debug ` image to allow remote viewing and control of the Chrome browser in the container. 

The environment variable `VNC_NO_PASSWORD=1` seems to indicate that a password is not required to connect to this VNC server, making it easy to connect and debug.








# <span style='color:#0fb9b1'>Mock Services</span>

## <span style='color:#20bf6b'>Fake-address-validation-server </span><mark style='background:#ABF7F7A6'>fake-address-validation (ghcr.io)</mark>

```yaml
fake-address-validation-server:
	image: ghcr.io/whiteboxservices/fake-address-validation:1.0.3
	networks:
		- test
		- inside
	environment:
		- PORT=8083
```

It has 2 networks avaliable:
- inside
- test


## <span style='color:#20bf6b'>Fake-bank-soap-server</span> <mark style='background:#ABF7F7A6'>fake-banking-flatex (grcr.io)</mark>

```yaml
fake-bank-soap-server:
	image: ghcr.io/whiteboxservices/fake-banking-flatex:1.0.14
	init: true
	networks:
		- test
	environment:
		- LOGGING=true
```

`LOGGING=true` --> The events are logged

It has 1 network avaliable:
- test


## <span style='color:#20bf6b'>Fake-iban-validation-server</span> <mark style='background:#ABF7F7A6'>fake-iban-validation (ghcr.io)</mark>

```yaml
fake-iban-validation-server:
	image: ghcr.io/whiteboxservices/fake-iban-validation:1.0.3
	init: true
	networks:
		- test
	environment:
		- LOGGING=true
```

It has 1 network avaliable:
- test


## <span style='color:#20bf6b'>Fake-idnow-server</span> <mark style='background:#ABF7F7A6'>fake-know-your-customer (ghcr.io)</mark>

```yaml
fake-idnow-server:
	image: ghcr.io/whiteboxservices/fake-know-your-customer:1.0.3
	networks:
		- inside
	environment:
		- PORT=8084
		- CALLBACK_URL=http://web:5000/identification/result
		- REDIRECT_SUCCESSFUL_URL=http://inside.whitebox.local:3000/identification/successful
		- REDIRECT_FAILED_URL=http://inside.whitebox.local:3000/identification/failed
```

`CALLBACK_URL=http://web:5000/identification/result:` --> 
Defines the URL to which the application will send the identification results.

`REDIRECT_SUCCESSFUL_URL=http://inside.whitebox.local:3000/identification/successful` -->
Defines the URL to which the user will be redirected if the identification is successful.

`REDIRECT_FAILED_URL=http://inside.whitebox.local:3000/identification/failed` --> 
Defines the URL to which the user will be redirected if the identification fails.

Know Your Customer (**KYC**) service simulation, which is a process used by companies to verify the identity of their customers. 


## <span style='color:#20bf6b'>Fake-idnow-server-test</span> <mark style='background:#ABF7F7A6'>fake-know-your-customer (ghcr.io)</mark>

```yaml
fake-idnow-server-test:
	image: ghcr.io/whiteboxservices/fake-know-your-customer:1.0.3
	networks:
		- test
	environment:
		- PORT=8082
		- CALLBACK_URL=http://inside.whitebox.local:3000/identification/result
		- REDIRECT_SUCCESSFUL_URL=http://inside.whitebox.local:3000/identification/successful
		- REDIRECT_FAILED_URL=http://inside.whitebox.local:3000/identification/failed
```

The main difference with the previous fake-idnow-server service is the network it connects to (test instead of inside) and the port it listens on (8082 instead of 8084). For testing purposes.


## <span style='color:#20bf6b'>Fake-twilio-server</span> <mark style='background:#ABF7F7A6'>fake-comunication (ghcr.io)</mark>

```yaml
fake-twilio-server:
	image: ghcr.io/whiteboxservices/fake-communication:1.0.4
	networks:
		- inside
	environment:
		- PORT=8085
```

It has 1 network avaliable:
- inside

A [Twilio](https://www.twilio.com/en-us) server Mock. 
Twilio is a cloud communications platform that enables adding voice, video, messaging and authentication capabilities to applications through Twilio APIs.


## <span style='color:#20bf6b'>Fake-twilio-server-test</span> <mark style='background:#ABF7F7A6'>fake-comunication (ghcr.io)</mark>

```yaml
fake-twilio-server-test:
	image: ghcr.io/whiteboxservices/fake-communication:1.0.4
	networks:
		- test
	environment:
		- PORT=8086
```

The main difference with the previous fake-twilio-server service is the network it connects to (test instead of inside) and the port it listens on (8086 instead of 8085). For testing purposes.

It has 1 network avaliable:
- test


## <span style='color:#20bf6b'>Fake-sftp-file-server</span> <mark style='background:#ABF7F7A6'>sftp</mark> 

```yaml
fake-sftp-file-server:
	image: atmoz/sftp
	networks:
		- test
	init: true
	command: test:password:::uploads
```

It has 1 network avaliable:
- test

`command: test:password:::uploads` --> 
This command is used to create an SFTP user with name "test", password "password", no access to SUDO and with its home directory set to "uploads".

SFTP server Mock. Testing purposes.



# Taiga Docker

This document intends to explain how to get Taiga up, using `docker` and `docker compose`.

You will find basic **configuration variables** in the `.env` file.

## Getting Started

Start the application:

```sh
docker-compose up -d
```

You can also make it this way:

```sh
./launch-all.sh
```

After some instants, when the application is started you can proceed to create the superuser with the following script:

```sh
./taiga-manage.sh createsuperuser
```

The `taiga-manage.sh` script lets launch `manage.py` commands on the back instance:

```sh
./taiga-manage.sh [COMMAND]
```

You can access the application in http://localhost:9000.

## Database settings

These vars are used to create the database for Taiga and connect to it.

```bash
POSTGRES_USER=taiga  # user to connect to PostgreSQL
POSTGRES_PASSWORD=taiga  # database user's password
```

## URLs settings

These vars set where your Taiga instance should be served, and the security protocols to use in the communication layer.

```bash
TAIGA_SCHEME=http  # serve Taiga using "http" or "https" (secured) connection
TAIGA_DOMAIN=localhost:9000  # Taiga's base URL
SUBPATH=""  # it'll be appended to the TAIGA_DOMAIN (use either "" or a "/subpath")
WEBSOCKETS_SCHEME=ws  # events connection protocol (use either "ws" or "wss")
```

The default configuration assumes Taiga is being served in a **subdomain**. For example:

```bash
TAIGA_SCHEME=https
TAIGA_DOMAIN=taiga.mycompany.com
SUBPATH=""
WEBSOCKETS_SCHEME=wss
```

If Taiga is being served in a **subpath**, instead of a subdomain, the configuration should be something like this:

```bash
TAIGA_SCHEME=https
TAIGA_DOMAIN=mycompany.com
SUBPATH="/taiga"
WEBSOCKETS_SCHEME=wss
```

## Secret Key settings

This variable allows you to set the secret key in Taiga, used in the cryptographic signing.

```bash
SECRET_KEY="taiga-secret-key"  # Please, change it to an unpredictable value!
```

## Email Settings

By default, emails will be printed in the standard output (`EMAIL_BACKEND=console`). If you have your own SMTP service, change it to `EMAIL_BACKEND=smtp` and configure the rest of these variables with the values supplied by your SMTP provider:

```bash
EMAIL_BACKEND=console  # use an SMTP server or display the emails in the console (either "smtp" or "console")
EMAIL_HOST=smtp.host.example.com  # SMTP server address
EMAIL_PORT=587   # default SMTP port
EMAIL_HOST_USER=user  # user to connect the SMTP server
EMAIL_HOST_PASSWORD=password  # SMTP user's password
EMAIL_DEFAULT_FROM=changeme@example.com  # email address for the automated emails

# EMAIL_USE_TLS/EMAIL_USE_SSL are mutually exclusive (only set one of those to True)
EMAIL_USE_TLS=True  # use TLS (secure) connection with the SMTP server
EMAIL_USE_SSL=False  # use implicit TLS (secure) connection with the SMTP server
```

## Queue manager settings

These variables are used to leave messages in the rabbitmq services.

```bash
RABBITMQ_USER=taiga  # user to connect to RabbitMQ
RABBITMQ_PASS=taiga  # RabbitMQ user's password
RABBITMQ_VHOST=taiga  # RabbitMQ container name
RABBITMQ_ERLANG_COOKIE=secret-erlang-cookie  # unique value shared by any connected instance of RabbitMQ
```

## Attachments settings

You can configure how long the attachments will be accessible by changing the token expiration timer. After that amount of seconds the token will expire, but you can always get a new attachment url with an active token.

```bash
ATTACHMENTS_MAX_AGE=360  # token expiration date (in seconds)
```

## Telemetry Settings

Telemetry anonymous data is collected in order to learn about the use of Taiga and improve the platform based on real scenarios. You may want to enable this to help us shape future Taiga.

```bash
ENABLE_TELEMETRY=True
```

You can opt out by setting this variable to False. By default, it's True.

## Session cookies in Django Admin

Taiga doesn't use session cookies in its API as it stateless. However, the Django Admin (`/admin/`) uses session cookie for authentication. By default, Taiga is configured to work behind HTTPS. If you're using HTTP (despite the strong recommendations against it), you'll need to configure the following environment variables so you can access the Admin:

Add to `&default-back-environment` environments

```yml
SESSION_COOKIE_SECURE: "False"
CSRF_COOKIE_SECURE: "False"
```

More info about those variables can be found [here](https://docs.djangoproject.com/en/3.1/ref/settings/#csrf-cookie-secure).

## Public registration

Public registration is disabled by default. If you want to allow a public register, you have to enable public registration on both, frontend and backend.

> **Note**
> Be careful with the upper and lower case in these settiings. We will use 'True' for the backend and 'true' for the frontend (this is not a typo, otherwise it won't work).


Add to `&default-back-environment` environments
```yml
PUBLIC_REGISTER_ENABLED: "True"
```

Add to `taiga-front` service environments
```yml
PUBLIC_REGISTER_ENABLED: "true"
```

> **Important**:
>
> Taiga (in its default configuration) disables both Gitlab or Github oauth buttons whenever the public registration option hasn't been activated. To be able to use Github/Gitlab login/registration, make sure you have public registration activated on your Taiga instance.

## GitHub OAuth login

Used for login with Github. This feature is disabled by default.

Follow the documentation ([GitHub - Creating an OAuth App](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/creating-an-oauth-app)) in Github, when save application Github displays the ID and Secret.

> **Note**
> Be careful with the upper and lower case in these settiings. We will use 'True' for the backend and 'true' for the frontend (this is not a typo, otherwise it won't work).

> **Note**
> `GITHUB_API_CLIENT_ID / GITHUB_CLIENT_ID` should have the same value.


Add to `&default-back-environment` environments
```yml
ENABLE_GITHUB_AUTH: "True"
GITHUB_API_CLIENT_ID: "github-client-id"
GITHUB_API_CLIENT_SECRET: "github-client-secret"
PUBLIC_REGISTER_ENABLED: "True"
```

Add to `taiga-front` service environments
```yml
ENABLE_GITHUB_AUTH: "true"
GITHUB_CLIENT_ID: "github-client-id"
PUBLIC_REGISTER_ENABLED: "true"
````

## Slack integration

Enable Slack integration in your Taiga instance. This feature is disabled by default.

> **Note**
> Be careful with the upper and lower case in these settiings. We will use 'True' for the backend and 'true' for the frontend (this is not a typo, otherwise it won't work).

Add to `&default-back-environment` environments
```yml
ENABLE_SLACK: "True"
```

Add to `taiga-front` service environments
```yml
ENABLE_SLACK: "true"
```

## GitHub importer

Activating this feature, you will be able to import projects from GitHub.

Follow this documentation ([GitHub - Creating an OAuth App](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/creating-an-oauth-app)) to obtain the _client id_ and the _client secret_ from GitHun.

> **Note**
> Be careful with the upper and lower case in these settiings. We will use 'True' for the backend and 'true' for the frontend (this is not a typo, otherwise it won't work).

Add to `&default-back-environment` environments
```yml
ENABLE_GITHUB_IMPORTER: "True"
GITHUB_IMPORTER_CLIENT_ID: "client-id-from-github"
GITHUB_IMPORTER_CLIENT_SECRET: "client-secret-from-github"
```

Add to `taiga-front` service environments
```yml
ENABLE_GITHUB_IMPORTER: "true"
```

## Jira Importer

Activating this feature, you will be able to import projects from Jira.

Follow this documentation ([Jira - OAuth 1.0a for REST APIs](https://developer.atlassian.com/cloud/jira/platform/jira-rest-api-oauth-authentication/)) to obtain the _consumer key_ and the _public/private certificate key_.


> **Note**
> Be careful with the upper and lower case in these settiings. We will use 'True' for the backend and 'true' for the frontend (this is not a typo, otherwise it won't work).

Add to `&default-back-environment` environments
```yml
ENABLE_JIRA_IMPORTER: "True"
JIRA_IMPORTER_CONSUMER_KEY: "consumer-key-from-jira"
JIRA_IMPORTER_CERT: "cert-from-jira"
JIRA_IMPORTER_PUB_CERT: "pub-cert-from-jira"
```

Add to `taiga-front` service environments
```yml
ENABLE_JIRA_IMPORTER: "true"
```

## Trello importer

Activating this feature, you will be able to import projects from Trello.

For configure Trello, you have two options:
- go to [https://trello.com/app-key](https://trello.com/app-key) (you must login first) and obtaing your development _API key_ and your _secret key_.
- or with the new method, [create a new Power-Up](https://developer.atlassian.com/cloud/trello/guides/rest-api/api-introduction/#managing-your-api-key) and generate an _API key_ and a _secret key_

> **Note**
> Be careful with the upper and lower case in these settiings. We will use 'True' for the backend and 'true' for the frontend (this is not a typo, otherwise it won't work).

Add to `&default-back-environment` environments
```yml
ENABLE_TRELLO_IMPORTER: "True"
TRELLO_IMPORTER_API_KEY: "api-key-from-trello"
TRELLO_IMPORTER_SECRET_KEY: "secret-key-from-trello"
```

Add to `taiga-front` service environments
```yml
ENABLE_TRELLO_IMPORTER: "true"
```

## Advanced configuration

The advanced configuration **will ignore** the environment variables in `docker-compose.yml` or `docker-compose-inits.yml`. Skip this section if you're using env vars.

It requires you to map the configuration files of `taiga-back` and `taiga-front` services to local files in order to unlock further configuration options.

**Map a `config.py` file**

From [taiga-back](https://github.com/kaleidos-ventures/taiga-back) download the file `settings/config.py.prod.example` and rename it:

```bash
mv settings/config.py.prod.example settings/config.py
```

Edit `config.py` with your own configuration:

- Taiga secret key: **it's important** to change it. It must have the same value as the secret key in `taiga-events` and `taiga-protected`
- Taiga urls: configure where Taiga would be served using `TAIGA_URL`, `SITES` and `FORCE_SCRIPT_NAME` (see examples below)
- Connection to PostgreSQL; check `DATABASES` section in the file
- Connection to RabbitMQ for `taiga-events`; check "EVENTS" section in the file
- Connection to RabbitMQ for `taiga-async`; check "TAIGA ASYNC" section in the file
- Credentials for email; check "EMAIL" section in the file
- Enable/disable anonymous telemetry; check "TELEMETRY" section in the file

Example to configure Taiga in **subdomain**:
```python
TAIGA_SITES_SCHEME = "https"
TAIGA_SITES_DOMAIN = "taiga.mycompany.com"
FORCE_SCRIPT_NAME = ""
```

Example to configure Taiga in **subpath**:
```python
TAIGA_SITES_SCHEME = "https"
TAIGA_SITES_DOMAIN = "taiga.mycompany.com"
FORCE_SCRIPT_NAME = "/taiga"
```

Check as well the rest of the configuration if you need to enable some advanced features.

Map the file into `/taiga-back/settings/config.py`. Have in mind that you have to map it both in `docker-compose.yml` and `docker-compose-inits.yml`. You can check the `x-volumes` section in docker-compose.yml with an example.

**Map a `conf.json` file**

From [taiga-front](https://github.com/kaleidos-ventures/taiga-front) download the file `dist/conf.example.json` and rename it:

```bash
mv dist/conf.example.json dist/conf.json
```

Edit it with your own configuration:

- Taiga urls: configure where Taiga would be served using `api`, `eventsUrl` and `baseHref` (see examples below)

Example of `conf.json` to serve Taiga in a **subdomain**:
```json
{
    "api": "https://taiga.mycompany.com/api/v1/",
    "eventsUrl": "wss://taiga.mycompany.com/events",
    "baseHref": "/",
```

Example of `conf.json` to serve Taiga in **subpath**:
```json
{
    "api": "https://mycompany.com/taiga/api/v1/",
    "eventsUrl": "wss://mycompany.com/taiga/events",
    "baseHref": "/taiga/",
```

Check as well the rest of the configuration if you need to enable some advanced features.

Map the file into `/taiga-front/dist/config.py`.

## Configure an admin user

```bash
docker compose up -d

docker compose -f docker-compose.yml -f docker-compose-inits.yml run --rm taiga-manage createsuperuser
```

## Up and running

Once everything has been installed, launch all the services and check the result:

```bash
docker compose up -d
```

## Configure the proxy

Your host configuration needs to make a proxy to `http://localhost:9000`.

If Taiga is being served in a **subdomain**:
```
  server {
      server_name taiga.mycompany.com;

      location / {
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Scheme $scheme;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_redirect off;
        proxy_pass http://localhost:9000/;
      }

      # Events
      location /events {
        proxy_pass http://localhost:9000/events;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_connect_timeout 7d;
        proxy_send_timeout 7d;
        proxy_read_timeout 7d;
      }

      # TLS: Configure your TLS following the best practices inside your company
      # Logs and other configurations
  }
```

If Taiga is being served in a **subpath** instead of a subdomain, the configuration should be something like:
```
server {
  server_name mycompany.com;

  location /taiga/ {
    proxy_set_header Host $http_host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Scheme $scheme;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_redirect off;
    proxy_pass http://localhost:9000/;
  }

  # Events
  location /taiga/events {
    proxy_pass http://localhost:9000/events;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_set_header Host $host;
    proxy_connect_timeout 7d;
    proxy_send_timeout 7d;
    proxy_read_timeout 7d;
  }

  # TLS: Configure your TLS following the best practices inside your company
  # Logs and other configurations
}
```

## Change between subpath and subdomain

If you're changing Taiga configuration from default subdomain (https://taiga.mycompany.com) to subpath (http://mycompany.com/subpath) or vice versa, on top of adjusting the configuration as said above, you should consider changing the TAIGA_SECRET_KEY so the refresh works properly for the end user.

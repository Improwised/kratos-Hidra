# ORY Kratos as Login Provider for ORY Hydra

## Prerequisite

* Download [Ngrok](https://www.google.com) and run Ngrok at http 4444

```bash
$ ./ngrok http 4444
```

* Copy forwarding url ending with .io which will be something like this

```
Forwarding                    http://d0ab99752edf.ngrok.io -> http://localhost:4444  
```

## Setup

* Go to `/contrib/hydra/kratos/`

* Open .kratos.yml file

* In the providers list change hydra's issuer url to ngrok forwarding url copied earlier with forward slash at the end.

```bash
# Example
issuer_url: http://d0ab99752edf.ngrok.io/
```

* Go to `/contrib/hydra/`

* Open docker-compose.yml file

* Under hydra change URLS_SELF_ISSUER, URLS_SELF_PUBLIC to ngrok forwarding url copied earlier with forward slash at the end.

```bash
# Example
      - URLS_SELF_ISSUER=http://d0ab99752edf.ngrok.io/
      - URLS_SELF_PUBLIC=http://d0ab99752edf.ngrok.io/
```

* Go to `/contrib/hydra/` and run docker-compose.yml.

```shell script
$ docker-compose up --build
```

* Next, create an OAuth2 Client

```shell script
$ docker-compose exec hydra \
    hydra clients create \
    --endpoint http://127.0.0.1:4445 \
    --id kratos-client \
    --secret kratos-secret \
    --grant-types authorization_code,refresh_token \
    --response-types code,id_token \
    --scope openid,offline \
    --callbacks http://127.0.0.1:3000/.ory/kratos/public/self-service/methods/oidc/callback/hydra
```

* Kratos UI will be running on http://127.0.0.1:3000

* Register new account, this will create account in kratos.

* Once register, log-in with hydra by clicking continue with hydra and it will redirect you to same login screen than sign in to kratos account.

* It will redirect you to the consent page and further login to account using hydra client.

# WAT

This boilerplate focuses on providing a simple way to run nextcloud on docker with SSL.
- It includes: postgres 13 and redis for production setups.
- It includes SSL support via LetsEncrypt using Traefik (either via http or dns challenge). 

It should be production ready.
Setup time: 1 minute

# Usage


```bash
cp .env_exmple .env
```

If you are just trying out, you can already run `./start.sh` and access via `https://mynextcloud.lan`

## Production setup

Adjust all the `passwords` (generate those) and the `DOMAIN` to your likings
```
vi .env
DB_PW=somepassword
REDIS_PW=somepassword12
DOMAIN=mynextcloud.lan
```

Now you can run it already (or jump to ssl DNS if you need DNS bases challenged below)

```
docker-compose up

# or

./start.sh
```

That's it - now connect to `https://mynextcloud.lan` or whatever you picked as the domain.

## SSL with traefik

#### HTTP-01 (default)
To be able to run nextcloud behind SSL using LetsEncrypt without any big effort, you can use the `traefik` integration.
The default configuration is already using

```
COMPOSE_FILE=docker-compose.yml:docker-compose-traefik-http.yml
```

Now you can already connect using `https://nextcloud.tld` and in addition requests to `http://nextcloud.tld` are already
redirected to `https://nextcloud.tld` automatically. Neat

#### DNS-01 (alternative for e.g. private/isolated networks)

Or for the DNS-01 variant you will need to add another 2 variables (this example is for cloudflare)

`vim .env`
```
# this is needed to enable the extra traefik service
COMPOSE_FILE=docker-compose.yml:docker-compose-traefik-dns.yml
TRAEFIK_ACME_CHALLENGE_DNS_PROVIDER=cloudflare
TRAEFIK_ACME_CHALLENGE_DNS_CREDENTIALS=CF_DNS_API_TOKEN=<YOURTOKEN>
```

```
docker-compose up
```

# Data

## Volumes
You nextcloud data is on the volume `data`
The database is located in the named volume `db`


# Update

This will update the wordpress / mysql image and restart your stack if there have been any. This will never delete
any of your data, plugins, themes or database ( what so ever )

just run

```bash
./update.sh
```

or manually

```bash
docker-compose pull
./start.sh
```

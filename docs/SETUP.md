# Setup guide

[Back to README](../README.md) · [Troubleshooting](TROUBLESHOOTING.md)

This guide covers the server and app separately. **If someone else maintains your TeslaMate server, ask them for the connection details rather than changing its configuration.** Examples use placeholder domains and passwords; they are not working public services.

## 1. Start with a working TeslaMate installation

For a new installation, follow the official [TeslaMate Docker guide](https://docs.teslamate.org/docs/installation/docker/). Use its current prerequisites and authentication instructions. Keep the machine running and verify the car and recent history in TeslaMate before proceeding.

For an existing installation, back up the database and configuration first. Do not replace its Compose file, change its database version, delete volumes or change its encryption key just to add Tesstats. The commands below run **in your existing Compose project directory**.

Check the services:

```sh
docker compose ps
```

TeslaMate, its database and MQTT broker should be running. Grafana is useful for checking recorded history, but its URL is not the API URL Tesstats needs.

## 2. Choose how the phone reaches your server

| Route | Use case | What is still required |
|---|---|---|
| LAN or private VPN | Access at home, or privately while away | A hostname reachable through that network and a secure MQTT endpoint. Keep the VPN connected when needed. |
| HTTPS/WSS reverse proxy | Access via a domain from mobile data | Valid TLS, authentication, correctly forwarded WebSockets and a hardened proxy. |
| Native MQTT/TLS | You already maintain a TLS broker | Reachable secure listener, commonly port 8883; HTTPS for history separately. |

A VPN does not change the app's MQTT transport: Tesstats still uses **mqtts or wss**, not plaintext port 1883. Avoid exposing PostgreSQL, anonymous MQTT, Grafana or the TeslaMate login just for this app.

The worked example below assumes Mosquitto 2.x and Caddy on the **same trusted Docker network** as TeslaMate, with service names `mosquitto`, `database` and `teslamateapi`. Adapt names if yours differ. Do not add a second proxy on ports 80/443 if an existing one already owns them.

## 3. Prepare a read-only MQTT login

Keep TeslaMate's current publisher connection working. Add a separate authenticated WebSocket listener for Tesstats instead of replacing the broker with an unrelated broker that has no vehicle topics.

### 3.1 Find the active configuration and volume

Inspect your Compose file: the stock TeslaMate example may start Mosquitto with `mosquitto -c /mosquitto-no-auth.conf`. In that case, editing `/mosquitto/config/mosquitto.conf` alone has no effect.

Keep the existing Mosquitto configuration/data volume mounts. Edit files in the host directory backing the configuration mount, or through your container/volume administration tool if it is a named volume. Do not accidentally replace the data volume. Arrange for the broker command to load the file you edited:

```yaml
command: mosquitto -c /mosquitto/config/mosquitto.conf
```

### 3.2 Create a dedicated password file

These commands use the existing Compose service's mounted configuration volume and prompt for a password. The broker does not have to be running.

**Only if this new file does not exist**, create it:

```sh
docker compose run --rm --no-deps --entrypoint mosquitto_passwd mosquitto -c /mosquitto/config/tesstats-passwd tesstats
```

If it already exists, add/update the account **without `-c`**:

```sh
docker compose run --rm --no-deps --entrypoint mosquitto_passwd mosquitto /mosquitto/config/tesstats-passwd tesstats
```

Never run `-c` on a password file you need to preserve: it overwrites that file. Ensure the broker's runtime user can read the result; avoid world-writable permissions. See the [Mosquitto password utility reference](https://mosquitto.org/man/mosquitto_passwd-1.html).

### 3.3 Restrict the app account to reading vehicle topics

Create `tesstats-acl` in the same mounted configuration directory:

```conf
user tesstats
topic read teslamate/cars/#
```

If your namespace is `home`, use `home/teslamate/cars/#` instead. The MQTT account is not your Tesla account and should not share its password.

### 3.4 Add the WebSocket listener

For a **stock, private Docker-only broker**, the following illustrates retaining the internal publisher listener while adding the app listener:

```conf
per_listener_settings true
persistence true
persistence_location /mosquitto/data/

# Existing TeslaMate publisher connection: private Docker network ONLY.
listener 1883
protocol mqtt
allow_anonymous true

# Only the trusted TLS reverse proxy should reach this listener.
listener 9001
protocol websockets
allow_anonymous false
password_file /mosquitto/config/tesstats-passwd
acl_file /mosquitto/config/tesstats-acl
```

**Do not publish ports 1883 or 9001 to the Internet or an untrusted network.** This example's internal listener has no authentication and assumes only trusted containers can reach it. If your publisher listener already uses passwords, ACLs or a security plugin, preserve that configuration instead of copying the anonymous listener above. Do not mix per-listener and global authentication rules without checking your broker version.

No Mosquitto host `ports:` mapping is needed when Caddy shares its Docker network. The public device connection is encrypted to Caddy; Caddy's internal connection to Mosquitto is plaintext within that trusted network. For a proxy on another host, secure that leg too.

After saving the configuration and password/ACL files:

```sh
docker compose up -d mosquitto
docker compose restart mosquitto
docker compose logs --tail=60 mosquitto
```

Check for unreadable files, invalid listeners or authentication errors, and confirm TeslaMate still publishes. Do not post the raw logs publicly without reviewing them.

See the [Mosquitto configuration reference](https://mosquitto.org/man/mosquitto-conf-5.html) for your installed version.

## 4. Add the history API

[TeslaMateApi's official setup](https://github.com/tobiasehlert/teslamateapi#how-to-use) describes its deployment and variables. Add the service to the **existing** Compose project/network, preserving the other services:

```yaml
services:
  teslamateapi:
    image: tobiasehlert/teslamateapi:latest
    restart: unless-stopped
    depends_on:
      - database
    environment:
      DATABASE_USER: ${TM_DB_USER}
      DATABASE_PASS: ${TM_DB_PASS}
      DATABASE_NAME: ${TM_DB_NAME}
      DATABASE_HOST: database
      ENCRYPTION_KEY: ${TM_ENCRYPTION_KEY}
      MQTT_HOST: mosquitto
      TZ: ${TM_TZ}
      ENABLE_COMMANDS: "false"
```

Merge the service under your existing `services:` key; do not paste a second top-level `services:` block. Define the five `TM_...` values in your private Compose `.env` file using the **existing** TeslaMate values. Do not invent a new encryption key. Use the same MQTT credentials/namespace as your existing server configuration if needed.

Do not publish port 8080 when the proxy can reach it directly. TeslaMateApi supports optional control features, but **Tesstats does not need them**: leave commands disabled. Its command token is not a substitute for protecting read endpoints with proxy authentication.

Start and check:

```sh
docker compose up -d teslamateapi
docker compose logs --tail=60 teslamateapi
```

The app uses a base ending in `/api` and appends paths such as `/v1/cars`. It does not connect directly to PostgreSQL.

## 5. Configure HTTPS and secure WebSockets

Skip this worked Caddy example if you already have an equivalent secure proxy. Adapt its routes instead.

### 5.1 Prepare a dedicated hostname

Use a hostname such as `tesstats.example.com` that resolves to your proxy from the phone's network. Obtain a valid certificate for that exact hostname. Caddy can manage public certificates when DNS and its certificate challenge are correctly configured. Private/VPN-only domains may require a different challenge or certificate setup; see [Caddy automatic HTTPS](https://caddyserver.com/docs/automatic-https).

Public-domain deployment may require forwarding TCP 80/443 to the proxy for the chosen challenge and HTTPS access. Do not open database/broker ports. If behind CGNAT or unable to configure safe ingress, use a private-network solution instead.

### 5.2 Add Caddy if you do not already have a proxy

This service must share the other services' network. Merge its named volumes with existing top-level volumes:

```yaml
services:
  caddy:
    image: caddy:2
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile:ro
      - tesstats-caddy-data:/data
      - tesstats-caddy-config:/config

volumes:
  tesstats-caddy-data:
  tesstats-caddy-config:
```

Create a regular file named `Caddyfile` in the Compose directory before starting this service. Generate a **separate proxy password hash**, entering the password interactively:

```sh
docker compose run --rm --no-deps --entrypoint caddy caddy hash-password
```

Keep the plain password in your password manager for the app. Put the generated hash, not the plain password, in the following Caddyfile. Replace the hostname and the `REPLACE_WITH_GENERATED_HASH` placeholder:

```caddyfile
tesstats.example.com {
    basic_auth {
        tesstats REPLACE_WITH_GENERATED_HASH
    }

    @mqtt path /mqtt /mqtt/*
    handle @mqtt {
        reverse_proxy mosquitto:9001
    }

    @history {
        method GET
        path /api /api/*
    }
    handle @history {
        reverse_proxy teslamateapi:8080
    }

    handle {
        respond "Not found" 404
    }
}
```

This is a dedicated app endpoint, not a replacement for the TeslaMate website. It authenticates both routes and only forwards GET requests to history. Do **not** use `handle_path /api/*`: stripping `/api` would break these upstream endpoints. Caddy handles WebSocket upgrades through `reverse_proxy`. References: [reverse proxy](https://caddyserver.com/docs/caddyfile/directives/reverse_proxy), [Basic Auth](https://caddyserver.com/docs/caddyfile/directives/basic_auth).

Validate and start:

```sh
docker compose run --rm --no-deps --entrypoint caddy caddy validate --config /etc/caddy/Caddyfile --adapter caddyfile
docker compose up -d caddy
docker compose logs --tail=60 caddy
```

If modifying an already running Caddyfile, validate then reload your existing proxy rather than deploying a duplicate.

## 6. Verify the endpoint before configuring the app

Use your real domain. This prompts for the **proxy** password without including it in shell history:

```sh
curl --fail --show-error --user tesstats https://tesstats.example.com/api/v1/cars
```

Expect JSON containing the vehicles, not a TeslaMate login page. Check without credentials too:

```sh
curl --silent --output /dev/null --write-out '%{http_code}\n' https://tesstats.example.com/api/v1/cars
```

For the example proxy, expect `401`. A public unauthenticated `200` means protection is missing. Do not attach the JSON response to support issues: it may identify your car. Avoid `curl -k`; a certificate error is something to fix, not bypass.

## 7. Enter the app settings

Open **Settings → Server** (the connection tab). For the worked example:

- Server / MQTT host: `tesstats.example.com` — hostname only.
- Port: `443`; transport: secure WebSocket / `wss`; path: `/mqtt`.
- MQTT username: `tesstats`; password: the **Mosquitto** password.
- Namespace: blank unless customised; enter only the namespace itself.
- TeslaMateApi base URL: `https://tesstats.example.com/api`.
- Basic Auth: enabled; username `tesstats`, password: the **Caddy** password.
- Custom-certificate trust and unencrypted access: **off**.

Tap **Test connection**, check both MQTT and API results, then **Save & Connect** or **Save**. Select the car and confirm Summary, Trips and Charging load. See the [README walkthrough](../README.md#connect-your-own-car).

## 8. Alternatives and certificate cautions

### Existing native MQTT/TLS listener

Choose native TLS transport, set the broker hostname and its TLS port (commonly `8883`), and use broker credentials. There is no WebSocket path and proxy Basic Auth does not authenticate native MQTT. Configure history over HTTPS separately.

The listener needs a valid certificate/key, a hostname matching the certificate, authentication and read-only permissions for the app user. Obtain the exact port and trust requirements from the administrator rather than copying a generic self-signed setup.

### Private HTTP history API

Only as an explicit exception on a network you control, the app can use a full URL such as `http://192.168.1.20:8080/api` after enabling **Allow unencrypted (LAN only)**. This label does not secure the connection or enforce a firewall; configuration can send credentials without TLS. Keep this off for public access. MQTT remains TLS/WSS even with this switch on.

### Custom/self-signed certificates

Prefer a properly trusted certificate. The app's custom-certificate toggle accepts connections after normal trust validation fails; it does **not** import or bind trust to one selected certificate. Do not enable it merely to dismiss an unexplained error.

Public-key pinning is an advanced alternative that requires a correctly derived, independently verified pin in the app's expected format. A certificate fingerprint and a public-key pin are not interchangeable. Leave the field empty if you do not have the correct value. See [Privacy and security](PRIVACY_AND_SECURITY.md).

## Final checklist

- [ ] TeslaMate still records data after the changes.
- [ ] MQTT accepts the app login and delivers vehicle topics.
- [ ] The history API returns cars over authenticated HTTPS.
- [ ] Unauthenticated requests are rejected.
- [ ] PostgreSQL and internal MQTT/WebSocket ports are not publicly exposed.
- [ ] The correct vehicle is selected and demo mode is off.
- [ ] Wi-Fi and mobile-data/VPN access both work where required.
- [ ] Secrets and server logs remain private.

These are reference configurations, not an automated installer or a security audit of your server. If a check fails, stop at that step and use [Troubleshooting](TROUBLESHOOTING.md).

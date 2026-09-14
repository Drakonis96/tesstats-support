# Setup guide

Tesstats normally uses two services from your own TeslaMate installation:

```text
Your Tesla → TeslaMate ─┬→ MQTT broker  → live vehicle values
                        └→ TeslaMateApi → trips, charges and analytics
```

## 1. Install TeslaMate

Follow the official [TeslaMate Docker installation guide](https://docs.teslamate.org/docs/installation/docker). Ensure TeslaMate is receiving data from the selected vehicle before configuring Tesstats.

## 2. Expose MQTT securely

Tesstats supports either native MQTT over TLS (`mqtts`) or MQTT over secure WebSockets (`wss`). Anonymous, unencrypted Internet-facing MQTT is unsafe and should never be used.

### Local network with TLS

Use this option when the phone and TeslaMate server share a trusted network.

1. Configure a TLS listener in Mosquitto, normally on port `8883`.
2. Require a dedicated username and strong password.
3. Make the listener reachable from the local network only.
4. In Tesstats, choose **MQTT over TLS**, enter the host, port and credentials.
5. Enable trust for a custom/self-signed certificate only if you created that certificate yourself and recognise its fingerprint.

Example Mosquitto listener:

```conf
per_listener_settings true
listener 8883
protocol mqtt
cafile /mosquitto/certs/server.crt
certfile /mosquitto/certs/server.crt
keyfile /mosquitto/certs/server.key
allow_anonymous false
password_file /mosquitto/config/passwd
```

### Remote access through a reverse proxy

Use a trusted reverse proxy such as Caddy, nginx or Traefik to expose:

- `wss://tesla.example.com/mqtt` → the Mosquitto WebSocket listener.
- `https://tesla.example.com/api` → TeslaMateApi.

Use a valid HTTPS certificate and protect the services with authentication. In Tesstats, select **WebSocket Secure**, port `443`, path `/mqtt`, and enter the MQTT credentials. If the proxy also uses Basic Auth, enable it in Tesstats and enter those separate credentials.

## 3. Add TeslaMateApi

[TeslaMateApi](https://github.com/tobiasehlert/teslamateapi) provides read-only history used for trips, charging sessions, battery health, cost calculations and charts.

- On a trusted LAN, enter its local URL and explicitly allow unencrypted LAN access only if HTTPS is unavailable.
- For remote access, expose it through HTTPS and enter its public base URL.

Live MQTT works without TeslaMateApi, but historical areas will be incomplete.

## 4. Configure Tesstats

Open **Settings → Server** and configure:

| Field | Purpose |
|---|---|
| MQTT host and port | Address of the secure MQTT listener |
| Transport | `mqtts` or `wss` |
| WebSocket path | Usually `/mqtt` when using `wss` |
| MQTT username/password | Dedicated Mosquitto credentials |
| Topic namespace | Leave empty unless TeslaMate uses a custom prefix |
| TeslaMateApi URL | Base URL for history and analytics |
| Basic Auth | Only when the reverse proxy requires it |

Tap **Test connection** before saving. A successful result should confirm both the live MQTT connection and the history API when configured.

## 5. Demo mode

To explore without a server, enable **Demo mode** from onboarding or Settings. Demo data does not contact a vehicle or network service.

## Security checklist

- Do not expose PostgreSQL to the Internet.
- Do not reuse your Tesla account password for MQTT or Basic Auth.
- Prefer HTTPS/WSS and a valid certificate for remote access.
- Restrict firewall ports to the services you actually use.
- Store secrets in server environment variables or secret storage, never in public issue reports.

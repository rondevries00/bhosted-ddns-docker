# bHosted DynDNS Docker (Ugreen)
Getest op Ugreen UGOS 1.16.0.0089

Kleine Docker Compose-container voor bHosted DynDNS.

De container controleert periodiek het actuele publieke IPv4-adres en roept bHosted alleen aan wanneer het IP-adres werkelijk is gewijzigd. Het laatst succesvol gemelde IP-adres wordt opgeslagen in `./data/last_successful_ip`, zodat de status behouden blijft na een container- of NAS-herstart.

## Werking

- Haalt het publieke IPv4-adres op via `https://api.ipify.org`.
- Vergelijkt dit met het laatst succesvol opgeslagen IP-adres.
- Controleert bij eerste start ook het huidige DNS A-record.
- Roept bHosted alleen aan bij een echte wijziging.
- Slaat het nieuwe IP pas op na een succesvolle bHosted-response.
- Standaard controle-interval: 900 seconden, dus 15 minuten.

## Bestanden

```text
.
├── compose.yaml
├── .env.example
├── .gitignore
├── README.md
├── LICENSE
├── data/
│   └── .gitkeep
└── docs/
    └── ugos-pro.md
```

## Installatie

### 1. Repository clonen of uploaden

Plaats deze map op je NAS, bijvoorbeeld:

```text
docker/bhosted-ddns
```

### 2. `.env` maken

Kopieer `.env.example` naar `.env`:

```bash
cp .env.example .env
```

Pas daarna `.env` aan:

```env
BHOSTED_HOST=home.example.com
BHOSTED_USER=jouw_bhosted_userid
BHOSTED_PASSWORD=VUL_HIER_HET_BHOSTED_MD5_WACHTWOORD_IN
TZ=Europe/Amsterdam
CHECK_INTERVAL_SECONDS=900
```

Gebruik voor `BHOSTED_PASSWORD` het speciale bHosted DynDNS MD5-wachtwoord/token, niet zomaar je normale accountwachtwoord.

### 3. Starten

```bash
docker compose up -d
```

### 4. Logs bekijken

```bash
docker logs -f bhosted-ddns
```

Of:

```bash
docker compose logs -f
```

## UGOS Pro

In UGOS Pro kun je dit als Docker Project gebruiken.

1. Open **Docker**.
2. Ga naar **Project**.
3. Kies **Create**.
4. Gebruik als projectnaam: `bhosted-ddns`.
5. Selecteer deze projectmap.
6. Zorg dat `compose.yaml` en `.env` in de projectmap staan.
7. Start/deploy het project.

Zie ook [`docs/ugos-pro.md`](docs/ugos-pro.md).

## Handmatige test

Wanneer de container draait:

```bash
docker exec bhosted-ddns /usr/local/bin/check-ddns.sh
```

Statusbestand bekijken:

```bash
cat data/last_successful_ip
```

## Interval aanpassen

In `.env`:

```env
CHECK_INTERVAL_SECONDS=900
```

Voorbeelden:

| Interval | Waarde |
|---|---:|
| 15 minuten | `900` |
| 10 minuten | `600` |
| 5 minuten | `300` |

Voor normaal DynDNS-gebruik is 15 minuten meestal prima.

## Verwijderen

```bash
docker compose down
```

Wil je ook de opgeslagen status verwijderen:

```bash
rm -f data/last_successful_ip
```

## Security notes

- De container heeft geen poortmapping nodig.
- De container gebruikt geen privileged mode.
- De container krijgt geen Docker socket.
- Het bHosted-wachtwoord staat alleen in `.env`; dit bestand wordt via `.gitignore` niet naar GitHub gecommit.
- Commit nooit je echte `.env` met wachtwoord/token naar GitHub.

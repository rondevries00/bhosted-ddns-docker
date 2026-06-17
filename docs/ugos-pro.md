# UGOS Pro installatie

## 1. Projectmap maken

Maak op je UGREEN NAS bijvoorbeeld deze map:

```text
docker/bhosted-ddns
```

Plaats daarin minimaal:

```text
compose.yaml
.env
```

De map `data` wordt gebruikt voor de runtime-status.

## 2. `.env` invullen

Maak `.env` op basis van `.env.example`:

```env
BHOSTED_HOST=jouwdomein.nl
BHOSTED_USER=jouwinloguserid
BHOSTED_PASSWORD=VUL_HIER_HET_BHOSTED_MD5_WACHTWOORD_IN
TZ=Europe/Amsterdam
CHECK_INTERVAL_SECONDS=900
```

Let op: gebruik niet automatisch je normale bHosted-wachtwoord. Voor DynDNS gebruikt bHosted normaal een speciale MD5-wachtwoordwaarde/token.

## 3. Docker Project maken

In UGOS Pro:

1. Open **Docker**.
2. Open **Project**.
3. Kies **Create** of **Maken**.
4. Projectnaam: `bhosted-ddns`.
5. Projectlocatie: de map `docker/bhosted-ddns`.
6. Gebruik de aanwezige `compose.yaml`.
7. Start/deploy het project.

## 4. Logs controleren

Open de container `bhosted-ddns` en controleer de logs.

Voorbeeld bij eerste start wanneer DNS al correct staat:

```text
bHosted DynDNS-container gestart
Hostnaam: home.rondevriesict.nl
Controle-interval: 900 seconden
Tijdzone: Europe/Amsterdam
[2026-06-17 22:30:00 CEST] Controle publiek IPv4-adres gestart
[2026-06-17 22:30:00 CEST] Eerste controle: DNS staat al correct op 86.89.84.210
[2026-06-17 22:30:00 CEST] Statusbestand aangemaakt; bHosted is niet aangeroepen
```

Voorbeeld bij wijziging:

```text
[2026-06-18 08:15:00 CEST] IP-wijziging vastgesteld: 86.89.84.210 -> 86.89.84.211
[2026-06-18 08:15:00 CEST] bHosted DynDNS wordt bijgewerkt
[2026-06-18 08:15:01 CEST] bHosted antwoord: good 86.89.84.211
[2026-06-18 08:15:01 CEST] Nieuw IP succesvol opgeslagen: 86.89.84.211
```

## 5. Belangrijk

- Verwijder de map `data` niet zomaar; daarin staat het laatst succesvol doorgegeven IP-adres.
- De container hoeft geen inkomende poorten te hebben.
- Na een NAS-herstart start de container automatisch opnieuw door `restart: unless-stopped`.

---
type: note
tags:
  - linux
  - studier
  - tools
---

# Linux Kommandoer

Spesielle stier: `.` her · `..` mappen over · `~` hjemmemappen · `-` forrige mappe · `/` root. 

---

## Hjelp

| Kommando                     | Gjør                                      |     |
| ---------------------------- | ----------------------------------------- | --- |
| `man <cmd>`                  | Manualside. `/tekst` søker, `q` avslutter |     |
| `man -k <ord>`               | Søk i alle manualsider                    |     |
| `<cmd> --help`               | Kort hjelp                                |     |
| `type <cmd>` / `which <cmd>` | Hva er det, og hvor ligger det?           |     |

---

## Viktige filer

| Fil                      | Innhold                                           |
| ------------------------ | ------------------------------------------------- |
| `/etc/passwd`            | Brukerkontoer. Lesbar for alle, **ingen passord** |
| `/etc/shadow`            | Passordhasher. Bare root                          |
| `/etc/group`             | Grupper og medlemmer                              |
| `/etc/sudoers`           | Hvem kan bruke `sudo`. Rediger med `visudo`       |
| `/etc/hosts`             | Statiske navn → IP, sjekkes **før** DNS           |
| `/etc/ssh/sshd_config`   | SSH-serverens oppsett                             |
| `~/.ssh/authorized_keys` | Nøkler som får logge inn som brukeren             |
| `/var/log/auth.log`      | Innlogginger, sudo, SSH                           |

## Navigasjon

| Kommando                               | Gjør                                    |     |
| -------------------------------------- | --------------------------------------- | --- |
| `pwd`                                  | Hvor er jeg?                            |     |
| `ls -lah`                              | Alt, lang visning, lesbare størrelser   |     |
| `ls -ltr`                              | Sortert etter tid, nyeste nederst       |     |
| `cd <mappe>` / `cd ..` / `cd` / `cd -` | Inn / opp / hjem / forrige              |     |


---

## Filer og mapper

| Kommando                     | Gjør                                        |
| ---------------------------- | ------------------------------------------- |
| `touch <fil>`                | Lag tom fil                                 |
| `mkdir -p a/b/c`             | Lag mappe, inkludert manglende mellomledd   |
| `cp <fra> <til>` / `cp -r`   | Kopier fil / mappe                          |
| `mv <fra> <til>`             | Flytt **eller** gi nytt navn                |
| `rm <fil>` / `rm -r <mappe>` | Slett. **Ingen papirkurv**                  |
| `rmdir <mappe>`              | Slett bare tom mappe - tryggere enn `rm -r` |
| `ln -s <mål> <lenke>`        | Symbolsk lenke                              |
| `file <fil>`                 | Filtype basert på innhold, ikke filendelse  |
| `stat <fil>`                 | Metadata og tidsstempler                    |

---

## Lese filer

| Kommando | Gjør |
|---|---|
| `cat <fil>` | Skriv ut hele fila |
| `less <fil>` | Bla. `/` søk, `G` slutt, `q` avslutt |
| `head -n 20` / `tail -n 20` | Første / siste 20 linjer |
| `tail -f <logg>` | **Følg** en logg live |
| `wc -l <fil>` | Tell linjer |
| `strings <binær>` | Lesbar tekst fra en binærfil |

---

## Finne ting

### find - etter navn og egenskaper

| Kommando                                          | Gjør                            |
| ------------------------------------------------- | ------------------------------- |
| `find . -name '*.txt'`                            | Etter navn, herfra og ned       |
| `find . -iname '*.txt'`                           | Uten å skille store/små         |
| `find /etc -type f`                               | Bare filer (`d` mapper)         |
| `find . -mtime -7`                                | Endret siste 7 dager            |
| `find . -size +100M`                              | Større enn 100 MB               |
| `find . -name '*.log' -exec grep -l 'error' {} +` | Kjør en kommando på hvert treff |

`2>/dev/null` fjerner «Permission denied»-dritten.

### grep - etter innhold

| Kommando | Gjør |
|---|---|
| `grep 'tekst' <fil>` | Søk |
| `grep -i` / `-r` / `-n` | Uten store/små · rekursivt · med linjenummer |
| `grep -v` | **Inverter** - linjer som *ikke* matcher |
| `grep -c` / `-l` | Tell treff / bare filnavn |
| `grep -E 'a\|b'` | Utvidet regex |
| `grep -rn --include='*.sh' 'curl' .` | Rekursivt, bare i `.sh`-filer |

`find` velger **hvilke filer**, `grep` ser **inni** dem.

---


### Mønster

```bash
# Hvilke IP-er har flest mislykkede SSH-innlogginger?
grep 'Failed password' /var/log/auth.log \
  | awk '{print $(NF-3)}' \
  | sort | uniq -c | sort -nr | head
```

`sort | uniq -c | sort -nr | head` = grupper, tell, sorter etter antall, vis toppen. Virker på alle loggformater.

---

## Brukere og grupper

| Kommando | Gjør |
|---|---|
| `whoami` / `id` | Hvem er jeg / UID, GID og grupper |
| `who` / `w` / `last` | Hvem er logget inn / hva de gjør / innloggingshistorikk |
| `su - <bruker>` | Bytt bruker (krever **deres** passord) |
| `sudo adduser <bruker>` | Lag bruker |
| `sudo passwd <bruker>` | Sett passord |
| `sudo usermod -aG sudo <bruker>` | **Legg til** i gruppe. Uten `-a` fjernes de andre gruppene |
| `sudo deluser <bruker>` | Slett bruker |

UID: `0` = root · `1-999` = systemkontoer · `1000+` = vanlige brukere. En annen konto enn root med UID 0 er et rødt flagg.

---

## Rettigheter og Eierskap

| | Verdi | Fil | Mappe |
|---|---|---|---|
| `r` | 4 | Lese | Liste innhold |
| `w` | 2 | Endre | Lage/slette filer i den |
| `x` | 1 | Kjøre | **Gå inn i** mappen |

| Modus | Betyr       | Bruk                           |
| ----- | ----------- | ------------------------------ |
| `755` | `rwxr-xr-x` | Programmer, mapper             |
| `644` | `rw-r--r--` | Vanlige filer                  |
| `600` | `rw-------` | Private filer - **SSH-nøkler** |
|       |             |                                |

| Kommando                         | Gjør                 |     |
| -------------------------------- | -------------------- | --- |
| `chmod 644 <fil>`                | Sett rettigheter     |     |
| `chmod u+x <fil>`                | Gi eier kjørerett    |     |
| `sudo chown bruker:gruppe <fil>` | Endre eier og gruppe |     |

---

## sudo

| Kommando      | Gjør                                                         |
| ------------- | ------------------------------------------------------------ |
| `sudo <cmd>`  | Kjør én kommando som root                                    |
| `sudo -l`     | **Hva har jeg lov til?** Første kommando på en ukjent maskin |
| `sudo -i`     | Root-shell                                                   |
| `sudo !!`     | Forrige kommando med sudo                                    |
| `sudo visudo` | Rediger sudoers trygt                                        |

`sudo` logger hvem som gjorde hva - det gjør ikke innlogging som root. En sudo-regel som bare tillater `vim` er i praksis full root, fordi vim kan starte et skall.

---

## Prosesser

| Kommando                         | Gjør                                               |
| -------------------------------- | -------------------------------------------------- |
| `ps aux`                         | Alle prosesser                                     |
| `top` / `htop`                   | Live oversikt                                      |
| `pgrep -a <navn>`                | Finn PID etter navn                                |
| `kill <PID>`                     | Avslutt (SIGTERM)                                  |
| `kill -9 <PID>`                  | Tving (SIGKILL). Siste utvei                       |
| `cmd &` / `Ctrl+Z` / `bg` / `fg` | Bakgrunn / pause / fortsett i bakgrunn / hent fram |
|                                  |                                                    |
Prøv alltid `kill` før `kill -9`.

---

## Tjenester (systemd)

| Kommando                                    | Gjør                             |
| ------------------------------------------- | -------------------------------- |
| `systemctl status <svc>`                    | Kjører den? Siste loggede linjer |
| `sudo systemctl start` / `stop` / `restart` | Styr den nå                      |
| `sudo systemctl enable --now <svc>`         | Start ved oppstart **og** nå     |
| `sudo systemctl disable <svc>`              | Ikke start ved oppstart          |
| `systemctl list-unit-files --state=enabled` | **Alt som starter automatisk**   |

`enabled` (starter ved oppstart) og `active` (kjører nå) er uavhengige av hverandre.

---

## Pakker

| Kommando                          | Gjør                                         |
| --------------------------------- | -------------------------------------------- |
| `sudo apt update`                 | Oppdater pakkelisten - installerer ingenting |
| `sudo apt upgrade`                | Oppgrader installerte pakker                 |
| `sudo apt install <pkg>`          | Installer                                    |
| `sudo apt remove <pkg>` / `purge` | Fjern / fjern med konfigurasjon              |
| `sudo apt autoremove`             | Fjern ubrukte avhengigheter                  |
| `apt search <ord>`                | Søk                                          |
| `dpkg -S <fil>`                   | Hvilken pakke eier fila?                     |
|                                   |                                              |

Fedora: `dnf` · Arch: `pacman`. 

---

## Nettverk

| Kommando                          | Gjør                                           |
| --------------------------------- | ---------------------------------------------- |
| `ip a`                            | Grensesnitt og IP-adresser                     |
| `ip r`                            | Rutetabell og standard gateway                 |
| `ip neigh`                        | ARP-tabell                                     |
| `ping -c 4 <vert>`                | Fire pings                                     |
| `traceroute <vert>`               | Veien gjennom nettet                           |
| `ss -tulpn`                       | **Hva lytter på maskinen, og hvilken prosess** |
| `dig +short <domene>`             | DNS-oppslag.                                   |
| `curl -I <url>`                   | Bare HTTP-headere                              |
| `nc -vz <vert> <port>`            | Er porten åpen?                                |
| `nmap -sV <vert>`                 | Skann porter og tjenester.                     |
| `sudo tcpdump -i eth0 -w ut.pcap` | Fang trafikk til [[Wireshark]]                 |

`ss -tulpn` = TCP, UDP, lytter, prosess, numerisk. 


### SSH

| Kommando                   | Gjør                                                  |
| -------------------------- | ----------------------------------------------------- |
| `ssh bruker@vert`          | Koble til                                             |
| `ssh-keygen -t ed25519`    | Lag nøkkelpar                                         |
| `ssh-copy-id bruker@vert`  | Legg nøkkelen på serveren                             |
| `scp fil bruker@vert:/sti` | Kopier fil                                            |

---

## System

| Kommando | Gjør |
|---|---|
| `df -h` | Ledig plass per filsystem |
| `du -sh * \| sort -h` | Størrelse på alt her, sortert |
| `lsblk` | Disker og partisjoner |
| `free -h` | Minne |
| `uname -r` | Kjerneversjon |
| `cat /etc/os-release` | Distribusjon og versjon |
| `uptime` | Oppetid og belastning |

---

## Hashing

| Kommando | Gjør |
|---|---|
| `sha256sum <fil>` | SHA-256-sjekksum |
| `sha256sum -c sums.txt` | Verifiser mot en liste |
| `base64 -d` | Dekod base64 |

**Hashing** = integritet (enveis). **Kryptering** = konfidensialitet (toveis). **Encoding** (base64) = ingen av delene, bare et annet format.

---

## Sikkerhet

`rm`, `dd`, `mkfs`, `chmod -R` og `chown -R` har ingen angreknapp.

- Kjør `ls` med samme mønster før `rm`
- Bruk `-n` hvis det finnes

---

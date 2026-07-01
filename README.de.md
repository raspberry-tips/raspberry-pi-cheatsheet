# Raspberry Pi Befehle Cheat Sheet

> 120+ Terminal-Befehle für Raspberry Pi OS, sortiert nach Themen – zum Nachschlagen und Kopieren.

📄 **PDF-Version zum Ausdrucken:** [raspberry-pi-befehle-cheat-sheet.pdf](https://cdn.raspberry.tips/2026/07/raspberry-pi-befehle-cheat-sheet.pdf)
🌐 **Vollständiger Artikel mit Kontext:** [raspberry.tips/raspberrypi-einsteiger/raspberry-pi-befehle-cheat-sheet](https://raspberry.tips/raspberrypi-einsteiger/raspberry-pi-befehle-cheat-sheet)
🇬🇧 **English version:** [README.md](README.md)

Ob frisch aufgesetzter Pi oder jahrelanger Dauerbetrieb: Irgendwann steht man vor dem Terminal und weiß den Befehl nicht mehr auswendig. Dieses **Raspberry Pi Befehle Cheat Sheet** sammelt über 120 Terminal-Kommandos für Raspberry Pi OS an einem Ort – von den ersten Schritten über GPIO und Kamera bis zu Bootloader-Updates und SD-Karten-Backup. Kein Tutorial zum Durchlesen, sondern eine Referenz zum Nachschlagen und Kopieren.

> **Was ist ein Raspberry Pi Cheat Sheet?**
>
> Ein Raspberry Pi Cheat Sheet ist eine kompakte Referenzliste der wichtigsten Terminal-Befehle für Raspberry Pi OS, sortiert nach Themen wie System, Netzwerk, GPIO oder Backup. Es ersetzt kein Tutorial, sondern dient als schnelles Nachschlagewerk für Befehle, die man sich nicht dauerhaft merken will oder muss.

> **So nutzt du dieses Cheat Sheet:** Alle Befehle sind nach Themen sortiert und direkt zum Kopieren gedacht. Platzhalter wie `<Pfad>` oder `<Dienst>` ersetzt du durch deine eigenen Werte. Befehle mit vorangestelltem `sudo` brauchen Admin-Rechte.

## Erste Schritte & System-Basics

Das Grundgerüst, das auf jedem frisch aufgesetzten Pi als Erstes gebraucht wird.

| Befehl | Was er macht |
| --- | --- |
| `sudo raspi-config` | Öffnet das zentrale, menügeführte Konfigurationstool: Spracheinstellung, Boot-Verhalten, Schnittstellen (SSH, I2C, SPI, Kamera), Übertaktung – die meisten Grundeinstellungen laufen hierüber. |
| `passwd` | Ändert das Passwort des aktuell angemeldeten Benutzers – fragt zuerst das alte, dann zweimal das neue Passwort ab. |
| `sudo reboot` | Startet den Pi sauber neu, alle laufenden Dienste werden vorher ordentlich beendet. |
| `sudo shutdown -h now` | Fährt den Pi sofort und sicher herunter (h = halt) – wichtig vor dem Ziehen des Stromkabels, sonst drohen SD-Karten-Schäden. |
| `sudo shutdown -r +5` | Kündigt allen angemeldeten Nutzern einen Neustart in 5 Minuten an, danach läuft er automatisch. |
| `whoami` | Gibt den Benutzernamen des aktuell aktiven Terminal-Nutzers aus – praktisch nach einem `su` oder `sudo -i`. |
| `hostname` | Zeigt nur den Gerätenamen (ohne Domain), unter dem sich der Pi im Netzwerk meldet. |
| `hostname -I` | Listet alle dem Pi zugewiesenen IPv4-/IPv6-Adressen (LAN, WLAN, VPN) in einer Zeile – siehe auch unser [ausführlicher Guide zur IP-Adresse](https://raspberry.tips/faq/raspberry-pi-ip-adresse-herausfinden). |
| `uname -a` | Gibt Kernel-Name, Version, Build-Datum und Architektur (z. B. aarch64) in einer Zeile aus – wichtig für Kompatibilitäts-Checks. |
| `cat /etc/os-release` | Zeigt Name, Versionsnummer und Codename des installierten Raspberry Pi OS – zuverlässiger als `uname` für die OS-Version. |
| `date` | Gibt aktuelles Datum und Uhrzeit inkl. Zeitzone aus, z. B. zum Abgleich mit Cronjob- oder Log-Zeitstempeln. |
| `timedatectl` | Zeigt lokale Zeit, UTC, Zeitzone und NTP-Sync-Status; mit `sudo timedatectl set-timezone` lässt sich die Zeitzone direkt ändern. |
| `uptime` | Zeigt Laufzeit seit dem letzten Boot sowie die Systemlast der letzten 1/5/15 Minuten (Load Average). |

## System-Info & Diagnose

`vcgencmd` ist Pi-exklusiv (Broadcom-Firmware-Schnittstelle) und funktioniert nicht auf normalem Debian.

| Befehl | Was er macht |
| --- | --- |
| `vcgencmd measure_temp` | Liest die SoC-Temperatur direkt vom Sensor aus, z. B. `temp=42.8'C` – ab 80 °C drosselt der Pi automatisch den Takt, ab 85 °C wird zusätzlich throttled. |
| `vcgencmd get_throttled` | Prüft, ob der Pi wegen zu wenig Strom oder zu hoher Temperatur gedrosselt wurde. `0x0` heißt: alles unauffällig – jeder andere Wert bedeutet, dass das aktuell oder seit dem letzten Start schon einmal passiert ist. |
| `vcgencmd measure_volts` | Zeigt standardmäßig die Spannung der Kernkomponente `core`; per Parameter auch `sdram_c`/`sdram_i`/`sdram_p` für die RAM-Bereiche. |
| `vcgencmd measure_clock arm` | Gibt die aktuelle CPU-Taktfrequenz in Hz aus – zum Abgleich mit dem in raspi-config eingestellten Übertaktungswert. |
| `vcgencmd get_config int` | Listet alle als Ganzzahl gesetzten config.txt-Parameter (z. B. `arm_freq`, `over_voltage`) mit ihrem aktuell aktiven Wert. |
| `vcgencmd version` | Zeigt Versionsnummer und Build-Datum der GPU-Firmware/Bootloader-Kombination – hilfreich beim Melden von Bugs. |
| `free -h` | Zeigt RAM- und Swap-Belegung in lesbaren Einheiten (h = MB/GB statt Bytes) inkl. verfügbarem und gepuffertem Speicher. |
| `df -h` | Zeigt belegten/freien Platz aller gemounteten Dateisysteme in lesbaren Einheiten – nützlich, um eine volle SD-Karte frühzeitig zu erkennen. |
| `du -sh <Ordner>` | Ermittelt die Gesamtgröße eines Ordners (s = summiert, h = lesbar) – gut zum Aufspüren von Speicherfressern vor dem Aufräumen. |
| `top` | Zeigt laufende Prozesse live, sortiert nach CPU-Auslastung – mit `q` beenden, mit `P`/`M` nach CPU bzw. RAM neu sortieren. |
| `htop` | Farbige, scrollbare Alternative zu `top` mit Maus-Bedienung und Balkenanzeige pro CPU-Kern (ggf. `sudo apt install htop`). |
| `lscpu` | Zeigt Kernanzahl, Architektur, Taktbereich und Cache-Größen der CPU in strukturierter Form. |
| `vcgencmd get_camera` | Gibt `supported=1 detected=1` aus, wenn ein Kameramodul korrekt erkannt und aktiviert wurde – erste Anlaufstelle bei Kamera-Problemen. |

> **Praxis-Tipp:** Für Dauerbetrieb und Backups (siehe Punkt 10) lohnt sich eine Karte mit hoher Schreibfestigkeit, z. B. die [Samsung Pro Endurance microSD](https://www.amazon.de/dp/B0D1CHNC49?tag=raspbertipps-21) – normale Consumer-Karten sind für 24/7-Logging nicht ausgelegt. Mehr dazu in unserem [SD-Karten-Vergleich](https://raspberry.tips/raspberrypi-einsteiger/die-richtige-sd-karte-fuer-den-raspberry-pi).

## Dateien & Verzeichnisse

| Befehl | Was er macht |
| --- | --- |
| `ls -la` | Listet alle Dateien inkl. versteckter (`-a`, beginnend mit Punkt) im Langformat (`-l`: Rechte, Besitzer, Größe, Datum). |
| `cd <Pfad>` | Wechselt das Arbeitsverzeichnis; `cd ..` geht eine Ebene hoch, `cd` ohne Argument zurück ins Home-Verzeichnis. |
| `pwd` | Gibt den vollständigen, absoluten Pfad des aktuellen Verzeichnisses aus (print working directory). |
| `cp <Quelle> <Ziel>` | Kopiert eine einzelne Datei – ein bereits existierendes Ziel wird ohne Rückfrage überschrieben. |
| `cp -r <Quelle> <Ziel>` | Kopiert einen kompletten Ordner mit allem, was darin liegt – auch verschachtelte Unterordner (dafür sorgt das Zusatz-Flag `-r`). Ohne `-r` bricht `cp` bei Ordnern mit einer Fehlermeldung ab. |
| `mv <Quelle> <Ziel>` | Verschiebt eine Datei/Ordner oder benennt sie um, wenn Quelle und Ziel im selben Verzeichnis liegen. |
| `rm <Datei>` | Löscht eine Datei unwiderruflich – es gibt keinen Papierkorb und kein Standard-Undo. |
| `rm -r <Ordner>` | Löscht einen Ordner mit allem, was darin liegt, auch verschachtelte Unterordner (`-r`) – in Kombination mit `-f` (force) erfolgt keine Rückfrage mehr, also besonders vorsichtig verwenden. |
| `mkdir <Name>` | Legt einen neuen, leeren Ordner an; mit `-p` werden fehlende übergeordnete Ordner gleich mit erstellt. |
| `touch <Datei>` | Erstellt eine leere Datei, falls sie nicht existiert, oder aktualisiert nur den Zeitstempel einer bestehenden Datei. |
| `nano <Datei>` | Öffnet die Datei im einsteigerfreundlichen Terminal-Editor – speichern mit `Strg+O`, beenden mit `Strg+X`. |
| `cat <Datei>` | Gibt den kompletten Dateiinhalt am Stück aus – bei langen Dateien unübersichtlich, dafür eignet sich `less` besser. |
| `less <Datei>` | Öffnet die Datei seitenweise zum Durchblättern (Pfeiltasten, `/` zum Suchen), ohne sie komplett in den Speicher zu laden. |
| `find <Pfad> -name "<Muster>"` | Durchsucht einen Ordner inklusive aller Unterordner nach Dateinamen, Muster erlaubt Platzhalter wie `*.log` für "alles, was auf .log endet". |
| `grep -r "<Text>" <Pfad>` | Durchsucht einen Ordner samt aller Unterordner (`-r`) nach einem Textstück und zeigt für jeden Treffer Dateiname plus die passende Zeile an. |
| `tar -czvf archiv.tar.gz <Ordner>` | Packt einen Ordner in ein komprimiertes Archiv (`c` = create, `z` = gzip, `v` = verbose, `f` = Dateiname). |
| `tar -xzvf archiv.tar.gz` | Entpackt ein gzip-komprimiertes tar-Archiv (`x` = extract) ins aktuelle Verzeichnis. |
| `chmod +x <Datei>` | Setzt das Ausführungsrecht für Besitzer, Gruppe und Andere – notwendig, damit Skripte direkt per `./skript.sh` gestartet werden können. |
| `chown pi:pi <Datei>` | Ändert Besitzer (`pi`) und Gruppe (`pi`) einer Datei – häufig nötig, wenn Dateien z. B. per FTP als root angelegt wurden. |

## Netzwerk & SSH

| Befehl | Was er macht |
| --- | --- |
| `ip a` | Zeigt alle Netzwerkschnittstellen (`eth0`, `wlan0`, `lo`) mit IPv4-/IPv6-Adressen und Status (UP/DOWN) – detaillierter als `hostname -I`. |
| `ping <Adresse>` | Sendet ICMP-Pakete an ein Ziel und misst die Antwortzeit – mit `Strg+C` beenden, mit `-c 4` auf 4 Pakete begrenzen. |
| `nmcli device wifi list` | Listet alle in Reichweite sichtbaren WLAN-Netzwerke mit Signalstärke und Verschlüsselungstyp auf. |
| `nmcli device wifi connect "<SSID>" password "<Passwort>"` | Verbindet sich direkt per Kommandozeile mit einem WLAN, ohne raspi-config zu öffnen. |
| `iwgetid` | Zeigt die SSID des aktuell verbundenen WLANs – schnellster Check, ob überhaupt eine WLAN-Verbindung besteht. |
| `sudo raspi-config nonint do_ssh 0` | Aktiviert SSH nicht-interaktiv per Skript (0 = an, 1 = aus) – ideal, um Headless-Setups zu automatisieren, siehe auch unser [SSH-Tutorial](https://raspberry.tips/raspberrypi-tutorials/ssh-am-raspberry-pi-aktivieren-neue-methode-mit-raspbian-25-11-2016). |
| `ssh pi@<IP-Adresse>` | Baut eine verschlüsselte Terminal-Verbindung zum Pi auf – beim ersten Verbinden muss der Host-Fingerprint bestätigt werden. |
| `scp <Datei> pi@<IP>:<Zielpfad>` | Kopiert eine Datei verschlüsselt über SSH auf den Pi, Syntax wie `cp`, nur mit Host-Adresse davor. |
| `ssh-keygen -t ed25519` | Erzeugt ein modernes, sicheres SSH-Schlüsselpaar – Public Key anschließend mit `ssh-copy-id` auf den Pi übertragen für passwortlosen Login. |
| `curl -I <URL>` | Ruft nur die HTTP-Response-Header ab, ohne den Seiteninhalt herunterzuladen – schneller Test, ob ein Server erreichbar ist und welchen Status er liefert. |
| `wget <URL>` | Lädt eine Datei direkt ins aktuelle Verzeichnis herunter, unterstützt mit `-c` auch das Fortsetzen abgebrochener Downloads. |
| `sudo raspi-config nonint get_hostname` | Gibt den aktuell gesetzten Hostnamen aus – Gegenstück zu `do_hostname` zum Setzen eines neuen Namens. |

## Paketverwaltung (APT)

| Befehl | Was er macht |
| --- | --- |
| `sudo apt update` | Lädt die aktuellen Paketlisten von den konfigurierten Repositories – installiert noch nichts, ist aber Voraussetzung für upgrade/install. |
| `sudo apt upgrade -y` | Installiert für bereits vorhandene Pakete verfügbare neue Versionen, ohne neue Abhängigkeiten zu installieren oder Pakete zu entfernen (`-y` bestätigt automatisch). |
| `sudo apt full-upgrade -y` | Wie upgrade, darf aber zusätzlich Pakete entfernen oder neu installieren, wenn das für die Auflösung von Versionskonflikten nötig ist – empfohlen bei größeren OS-Updates. |
| `sudo apt install <Paket>` | Installiert ein neues Paket samt aller benötigten Abhängigkeiten. |
| `sudo apt remove <Paket>` | Deinstalliert das Programm, lässt aber Konfigurationsdateien in `/etc` unangetastet – praktisch bei einer späteren Neuinstallation. |
| `sudo apt purge <Paket>` | Entfernt Programm und Konfigurationsdateien vollständig – für einen wirklich sauberen Neustart der Installation. |
| `sudo apt autoremove` | Entfernt automatisch installierte Abhängigkeiten, die von keinem verbleibenden Paket mehr benötigt werden. |
| `apt list --installed` | Zeigt alle über apt/dpkg installierten Pakete mit Versionsnummer – gut kombinierbar mit `\| grep <Name>`. |
| `apt-cache search <Begriff>` | Durchsucht Paketnamen und Kurzbeschreibungen der Repositories nach einem Stichwort, bevor man den genauen Paketnamen kennt. |
| `dpkg -l \| grep <Name>` | Prüft direkt über dpkg (die Ebene unter apt), ob und in welcher Version ein Paket installiert ist. |
| `sudo apt clean` | Löscht heruntergeladene .deb-Installationsdateien aus `/var/cache/apt/archives` – gibt bei knappem Speicher schnell Platz frei. |

## Prozesse & Dienste (systemd)

| Befehl | Was er macht |
| --- | --- |
| `systemctl status <Dienst>` | Zeigt, ob ein Dienst aktiv/inaktiv/fehlgeschlagen ist, seit wann, und die letzten Log-Zeilen direkt mit dazu. |
| `sudo systemctl start <Dienst>` | Startet einen Dienst sofort, ändert aber nichts am Autostart-Verhalten beim nächsten Boot. |
| `sudo systemctl stop <Dienst>` | Beendet einen laufenden Dienst sofort, ohne ihn dauerhaft zu deaktivieren. |
| `sudo systemctl restart <Dienst>` | Beendet und startet einen Dienst neu in einem Schritt – Standardvorgehen nach Konfigurationsänderungen. |
| `sudo systemctl enable <Dienst>` | Sorgt dafür, dass der Dienst bei jedem Systemstart automatisch mitgestartet wird. |
| `sudo systemctl disable <Dienst>` | Entfernt den Autostart-Eintrag – der Dienst muss danach manuell gestartet werden, läuft aber ggf. noch bis zum nächsten Neustart. |
| `systemctl list-units --type=service` | Listet alle aktuell geladenen Dienste mit Status (active/inactive/failed) auf. |
| `ps aux` | Zeigt alle laufenden Prozesse aller Benutzer mit PID, CPU-/RAM-Auslastung und vollem Befehl. |
| `ps aux \| grep <Name>` | Filtert die Prozessliste auf einen Namen – schnellster Weg, um die PID eines Programms zu finden. |
| `kill <PID>` | Bittet einen Prozess höflich, sich zu beenden – er darf vorher noch aufräumen (z. B. Dateien speichern), reagiert aber nicht immer sofort. |
| `kill -9 <PID>` | Beendet einen Prozess sofort und ohne Rücksicht, ohne ihm Zeit zum Aufräumen zu lassen – nur nutzen, wenn `kill` allein nicht reicht. |
| `killall <Name>` | Beendet alle Prozesse mit exakt diesem Namen gleichzeitig, ohne vorher die PID nachschlagen zu müssen. |
| `journalctl -xe` | Zeigt die letzten System-Logs mit erweiterten Erklärungen (`-x`) direkt am Ende des Logs (`-e`) – erster Schritt bei jedem Fehler nach dem Boot. |
| `journalctl -u <Dienst> -f` | Zeigt das Log eines einzelnen Dienstes live (`-f` = follow, wie `tail -f`) – ideal zum Debuggen während des Starts. |

## GPIO & Hardware-Schnittstellen

> **Tipp:** Für die Pin-Belegung selbst musst du dir nichts merken – unser interaktives [**GPIO-Pinout-Tool**](https://raspberry.tips/raspberry-pi-gpio-pinout) zeigt auf einen Klick Pin-Nummer, Funktion und Board-Position für Pi 5, 4, 3 und Zero. Die Befehle unten sind für den direkten Zugriff im Terminal.

> **Hinweis:** Der klassische Befehl `gpio readall` aus der WiringPi-Bibliothek gilt als veraltet und ist in aktuellen Raspberry Pi OS-Repos nicht mehr enthalten. Für neue Projekte `raspi-gpio` verwenden. Mehr Hintergrund im [GPIO-Einsteiger-Tutorial](https://raspberry.tips/raspberrypi-einsteiger/raspberry-pi-gpio-pins-erklaert-das-ultimative-einsteiger-tutorial).

| Befehl | Was er macht |
| --- | --- |
| `raspi-gpio get` | Zeigt für alle Pins Funktion (Modus), Pull-Up/Down-Widerstand und aktuellen Level (0/1) in einer kompakten Übersicht. |
| `raspi-gpio get <Pin>` | Zeigt Modus, Pull-Widerstand und Level nur für den angegebenen Pin, z. B. `raspi-gpio get 17`. |
| `raspi-gpio set <Pin> op dh` | Konfiguriert den Pin als Ausgang (`op`) und schaltet ihn direkt auf High/3,3V (`dh` = drive high). |
| `raspi-gpio set <Pin> op dl` | Konfiguriert den Pin als Ausgang und schaltet ihn auf Low/0V (`dl` = drive low). |
| `raspi-gpio funcs` | Listet für jeden Pin alle verfügbaren alternativen Hardwarefunktionen (z. B. I2C, SPI, UART, PWM) auf. |
| `sudo raspi-config nonint do_i2c 0` | Aktiviert die I2C-Schnittstelle inkl. nötiger Kernel-Module, ohne das Menü zu öffnen (0 = an, 1 = aus). |
| `sudo raspi-config nonint do_spi 0` | Aktiviert die SPI-Schnittstelle nicht-interaktiv – Voraussetzung für viele Displays und ADC-Module. |
| `i2cdetect -y 1` | Scannt den I2C-Bus 1 (Standard bei aktuellen Modellen) und zeigt die Adressen aller erkannten Geräte in einer Tabelle. |
| `sudo raspi-config nonint do_serial_hw 0` | Aktiviert die Hardware-UART (`ttyAMA0`) für zuverlässige serielle Kommunikation, z. B. mit GPS-Modulen oder RTC-Boards. |

## Kamera-Modul

> **Hinweis:** Aktuelle Raspberry Pi OS-Versionen nutzen den `rpicam-*`-Befehlssatz (Nachfolger von `libcamera-*` und dem alten `raspistill`). Alte Befehlsnamen funktionieren teils noch, weil sie im Hintergrund automatisch zu den neuen weitergeleitet werden – in neuen Skripten sollte man sie trotzdem nicht mehr verwenden.

| Befehl | Was er macht |
| --- | --- |
| `rpicam-hello` | Öffnet für einige Sekunden eine Live-Vorschau der Kamera auf dem angeschlossenen Display – einfachster Funktionstest. |
| `rpicam-still --list-cameras` | Listet alle vom System erkannten Kameramodule mit Sensor-Namen und unterstützten Auflösungen auf. |
| `rpicam-still -o bild.jpg` | Nimmt nach kurzem Autofokus-/Belichtungsabgleich ein einzelnes Foto auf und speichert es unter dem angegebenen Namen. |
| `rpicam-vid -t 10000 -o video.h264` | Nimmt ein Video mit der in `-t` angegebenen Dauer in Millisekunden auf (hier: 10 Sekunden). |
| `vcgencmd get_camera` | Schneller Software-Check, ob die Kamera erkannt (`detected=1`) und im System aktiviert (`supported=1`) ist, ohne ein Bild aufzunehmen. |

## Bootloader, Firmware & Updates

Pi-exklusiv: EEPROM-Bootloader-Verwaltung gibt es nur auf echter Raspberry-Pi-Hardware (Pi 4/5).

| Befehl | Was er macht |
| --- | --- |
| `vcgencmd bootloader_version` | Zeigt Versionsdatum und Release-Kanal (critical/stable/beta) des aktuell geflashten EEPROM-Bootloaders. |
| `sudo rpi-eeprom-update` | Vergleicht die installierte mit der neuesten verfügbaren Bootloader-Version, ohne etwas zu verändern (reiner Check). |
| `sudo rpi-eeprom-update -a` | Spielt automatisch die neueste verfügbare EEPROM-Version ein – wird beim nächsten Neustart durch `recovery.bin` geschrieben. |
| `sudo raspi-config nonint do_expand_rootfs` | Vergrößert die Root-Partition auf die volle Kapazität der SD-Karte/SSD – bei den meisten aktuellen Images bereits automatisch beim ersten Boot erledigt. |
| `sudo apt update && sudo apt full-upgrade -y` | Kombinierter Befehl für ein komplettes System-Update inkl. Kernel- und Firmware-Paketen in einem Rutsch. |

## Storage, SD-Karte & Backup

> **Vorsicht bei `dd`:** `if=` (Input) und `of=` (Output) dürfen nicht vertauscht werden – ein Zahlendreher überschreibt die falsche Platte unwiderruflich. Laufwerksnamen vorher immer mit `lsblk` prüfen. Eine ausführliche Anleitung gibt es in unserem [Backup-Tutorial](https://raspberry.tips/raspberrypi-tutorials/raspberry-pi-datensicherung-erstellen).

| Befehl | Was er macht |
| --- | --- |
| `lsblk` | Zeigt alle Blockgeräte als Baumstruktur mit Partitionen, Größen und Mountpunkten – der sicherste erste Schritt vor jedem `dd`-Befehl. |
| `sudo fdisk -l` | Zeigt detaillierte Partitionstabellen (Typ, Start-/End-Sektor, Größe) aller erkannten Laufwerke. |
| `sudo mount /dev/sda1 /mnt` | Hängt die Partition sda1 manuell in das (leere) Verzeichnis `/mnt` ein, danach über `/mnt` zugreifbar. |
| `sudo umount /mnt` | Hängt ein zuvor gemountetes Laufwerk sauber aus – vor dem physischen Abziehen unbedingt nötig, um Datenverlust zu vermeiden. |
| `sudo dd if=/dev/sdX of=backup.img bs=4M status=progress` | Erstellt ein exaktes 1:1-Abbild der gesamten Karte inklusive Partitionstabelle; `bs=4M` beschleunigt die Übertragung, `status=progress` zeigt den Fortschritt live. |
| `sudo dd if=backup.img of=/dev/sdX bs=4M status=progress` | Schreibt ein zuvor erstelltes Image zurück auf eine Karte/SSD – überschreibt dabei restlos alle vorhandenen Daten auf dem Ziel. |
| `rsync -avh <Quelle> <Ziel>` | Gleicht zwei Ordner ab und überträgt nur, was neu oder geändert ist – deutlich schneller als jedes Mal alles komplett neu zu kopieren. `a` übernimmt dabei auch Rechte und Zeitstempel, `v` zeigt jede Datei einzeln an, `h` macht die Größenangaben lesbar (MB/GB statt Bytes). |

## Benutzer & Rechte

| Befehl | Was er macht |
| --- | --- |
| `sudo adduser <Name>` | Legt interaktiv einen neuen Benutzer inkl. Home-Verzeichnis an und fragt dabei Passwort sowie optionale Angaben ab. |
| `sudo deluser <Name>` | Entfernt einen Benutzer-Account; das Home-Verzeichnis bleibt standardmäßig erhalten (mit `--remove-home` komplett löschen). |
| `sudo usermod -aG sudo <Name>` | Fügt den Benutzer zur sudo-Gruppe hinzu, ohne ihn aus seinen bisherigen Gruppen zu entfernen (das bewirkt `-a`) – ohne dieses Flag würden alle anderen Gruppen-Mitgliedschaften verloren gehen. |
| `groups <Name>` | Zeigt alle Gruppen, in denen ein Benutzer Mitglied ist – z. B. um zu prüfen, ob er zu `gpio` oder `i2c` gehört. |
| `who` | Listet alle aktuell angemeldeten Benutzer mit Terminal und Login-Zeit auf, auch bei mehreren gleichzeitigen SSH-Sitzungen. |
| `sudo -i` | Öffnet eine Sitzung, in der du dauerhaft als Administrator (root) arbeitest, statt jedem einzelnen Befehl `sudo` voranzustellen – bis du sie mit `exit` wieder verlässt. Mit Vorsicht nutzen, hier gibt es keine Sicherheitsbremse mehr. |

## Tastenkombinationen & Terminal-Tricks

| Kombination | Was sie macht |
| --- | --- |
| `Strg + C` | Bricht den gerade laufenden Befehl sofort ab – der Not-Aus-Knopf im Terminal, wenn etwas hängt oder man es sich anders überlegt. |
| `Strg + Z` | Pausiert den Prozess und legt ihn in den Hintergrund – mit `fg` wieder in den Vordergrund holen, mit `bg` im Hintergrund weiterlaufen lassen. |
| `Strg + R` | Startet eine interaktive Rückwärtssuche durch die Befehlshistorie – Tippen filtert live, Enter führt aus. |
| `Tab` | Vervollständigt Befehle, Dateinamen und Pfade automatisch – zweimal drücken zeigt bei Mehrdeutigkeit alle Optionen. |
| `history` | Zeigt eine nummerierte Liste aller zuletzt ausgeführten Befehle – mit `!123` lässt sich Befehl Nummer 123 direkt erneut ausführen. |
| `!!` | Führt den zuletzt ausgeführten Befehl exakt noch einmal aus, ohne ihn erneut eintippen zu müssen. |
| `sudo !!` | Wiederholt den letzten Befehl mit vorangestelltem sudo – praktisch, wenn ein Befehl gerade wegen fehlender Rechte fehlgeschlagen ist. |
| `clear` | Leert die sichtbare Terminal-Ausgabe, der Scroll-Verlauf bleibt aber über die Bildlaufleiste erhalten. |
| `exit` | Beendet die aktuelle Shell-Sitzung – bei einer SSH-Verbindung wird dadurch auch die Verbindung zum Pi getrennt. |

Wer regelmäßig neue Skripte auf dem Pi laufen lässt, sollte auch einen Blick auf unseren [Autostart-Guide](https://raspberry.tips/raspberrypi-einsteiger/raspberry-pi-autostart-von-skripten-und-programmen-einrichten) werfen – damit Programme nach jedem Neustart automatisch starten, ohne dass ein Befehl von Hand nötig ist.

## FAQ

**Wie finde ich die IP-Adresse meines Raspberry Pi per Terminal heraus?**

Am schnellsten mit dem Befehl `hostname -I` direkt am Pi – er zeigt alle zugewiesenen IP-Adressen auf einen Blick. Alternativ liefert `ip a` eine detailliertere Übersicht aller Netzwerkschnittstellen.

**Warum funktioniert der Befehl gpio readall bei mir nicht mehr?**

Das `gpio`-Tool aus der WiringPi-Bibliothek wurde aus den offiziellen Raspberry Pi OS-Repositories entfernt, da WiringPi als Projekt lange nicht mehr aktiv gepflegt wurde. Als Ersatz dient `raspi-gpio`: `raspi-gpio get` zeigt den Zustand aller Pins, `raspi-gpio funcs` die verfügbaren alternativen Funktionen.

**Wie prüfe ich, ob mein Raspberry Pi durch Unterspannung gedrosselt wird?**

Mit `vcgencmd get_throttled` prüfen. `0x0` bedeutet: alles unauffällig. Jeder andere Wert zeigt, dass der Pi aktuell oder seit dem letzten Start schon einmal wegen zu wenig Strom gedrosselt wurde – meist ein Hinweis auf ein zu schwaches Netzteil oder ein schlechtes Kabel.

**Was ist der Unterschied zwischen apt upgrade und apt full-upgrade?**

`apt upgrade` aktualisiert nur bereits installierte Pakete und installiert dabei keine neuen oder entfernt bestehende. `apt full-upgrade` darf zusätzlich Pakete entfernen oder neu installieren, wenn das für die Auflösung von Abhängigkeiten bei größeren Updates nötig ist.

**Wie erstelle ich ein komplettes Backup meiner SD-Karte per Kommandozeile?**

Mit `sudo dd if=/dev/sdX of=backup.img bs=4M status=progress` lässt sich ein 1:1-Image der kompletten Karte erstellen. Wichtig: Vorher mit `lsblk` das richtige Laufwerk identifizieren, da `if=` und `of=` bei Verwechslung das falsche Ziel überschreiben.

---

## Lizenz

Dieses Cheat Sheet darf unter Angabe der Quelle (raspberry.tips) vervielfältigt, genutzt und weitergegeben werden – siehe [LICENSE](LICENSE) (CC BY 4.0).

Mehr Raspberry-Pi-Tutorials, Vergleiche und Tools: **[raspberry.tips](https://raspberry.tips)**

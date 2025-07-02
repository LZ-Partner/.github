- [LoccoZ AG Version Control System](#loccoz-ag-version-control-system)
  - [Organisation und Regeln](#organisation-und-regeln)
  - [SSH-Key](#ssh-key)
    - [Funktionsweise der SSH-Key-Authentifizierung](#funktionsweise-der-ssh-key-authentifizierung)
  - [Guide wie man SSH-Key auf Windows 11 einrichtet und in Gitlab hinterlegt](#guide-wie-man-ssh-key-auf-windows-11-einrichtet-und-in-gitlab-hinterlegt)
  - [Git in Powershell Aufsetzen](#git-in-powershell-aufsetzen)
  - [Commit Regeln und Konventionen](#commit-regeln-und-konventionen)
  - [Troubleshooting](#troubleshooting)

__DIESES DOKUMENT BEFINDET SICH NOCH IN DER ENTWICKLUNG__. 
Willkommen zur LoccoZ Organisation! Dieses Dokument dient der allgemeinen Einführung in das Version Controlling von Hardware und Software Projekten von Loccoz AG. Nebst dieser Einführung werden auch andere organisatorische Punkte angesprochen.

# LoccoZ AG Version Control System

__Momentan__ befinden sich alle Elektronik und Software Projekte im offiziellen GitLab Server als separate Subgruppen (siehe Abbildung) unter dieser [GitLab Instanz](https://gitlab.com/loccoz-system-ag). In der Zukunft möchten wir jedoch diese GitLab Instanz auf dem firmeninternen Server laufen lassen ([Self-Hosting Gitlab-Server](https://about.gitlab.com/install/)).

<div align="center">
    <img src="gitlab_organisation.svg"/>
</div>

Diese vereinfachte Schematik soll die generelle Strukturierung der Projekten (=Repositories) repräsentieren. Der Owner kann über die Rollenverteilung alle Rechte und Zugriffe zu der Gruppe, Subgruppen und den Projekten separat bestimmen.

## Organisation und Regeln
Für die detaillierte Erklärung der Rollen weise ich auf die [GitLab Dokumentation](https://docs.gitlab.com/user/permissions/) hin. Im Moment bin ich (Masiar Etemadi), der einzige, der aktiv zur Hardware- und Software Entwicklung beiträgt. Deswegen sind die Regeln für das Arbeiten so festgelegt: 

- Owner Groups und Subgroups = Masiar Etemadi
- Protection = Main branch ist so geschützt, dass nur der Owner Veränderungen im Repo pushen kann. Die anderen Personen können im Moment nur in eigens erschaffene Branches ihre Veränderungen pushen.

## SSH-Key
SSH-Keys (Secure Shell Keys) sind ein kryptografisches Schlüsselpaar, das zur sicheren Authentifizierung zwischen einem Client (z. B. deinem PC) und einem Server (z. B. GitLab, GitHub oder einem Remote-Server) verwendet wird.
Ein SSH-Key besteht aus zwei Teilen:
- Privater Key (`id_ed25519`): Geheimer Schlüssel, der __niemals weitergegeben__ werden darf. Sein Speicherort lautet `~/.ssh/id_ed25519`.
- Öffentlicher Key (`id_ed25519.pub`): Wird auf dem Server (z.B GitLab oder firmeninterner Server) hinterlegt. Sein Speicherort lautet `~/.ssh/id_ed25519.pub`

### Funktionsweise der SSH-Key-Authentifizierung
1. Schlüsselpaar generieren
    - Du erstellst ein Schlüsselpaar
    - Der private Key bleibt auf deinem Rechner.
    - Der public Key wird auf dem Server gespeichert.
2. Verbindungsaufbau
    - Client (dein PC) stellt eine Verbindung zum Server (GitLab) her.
    - Der Client signiert diese Nachricht mit dem privaten Key.
    - Der Server überprüft die Signatur mit dem hinterlegten öffentlichen Key.
3. Zugriff gewährt/verweigert

## Guide wie man SSH-Key auf Windows 11 einrichtet und in Gitlab hinterlegt

1. Öffne die Eingabeaufforderung (CMD) oder Windows Terminal (als Administrator). 
2. Prüfen, ob SSH installiert ist
     ```powershell
     ssh -V
     ```
    Falls SSH nicht installiert ist, installiere es über:
     ```powershell
     Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0
     ```
3. (Falls bereits einer existiert) Vorhandener SSH-Key verwernden
     ```powershell
     ls ~/.ssh/
     ```
4. Generiere einen neuen SSH-Key mit (Falls kein/neuer SSH-Key benötigt wird):
     ```powershell
     ssh-keygen -t ed25519 -C "deine-email@example.com" -f ~/.ssh/gitlab_key
     ```
     Ersetze deine-email@example.com mit deiner E-Mail-Adresse. Bestätige den Standardpfad `~\.ssh\id_ed25519` mit __Enter__. Beim Anfragen des Passphrases einfach nochmal __Enter__ drücke. Danach noch einmal __Enter__ drücken.

5. Starte den SSH-Agent:
     ```powershell
     Start-Service ssh-agent
     ```
     Füge den Key hinzu:
     ```powershell
     ssh-add ~/.ssh/gitlab_key
     ```
     Geladene Keys überprüfen:
     ```powershell
     ssh-add -l
     ```
6. SSH-Key in Gitlab hinterlegen
   1. Öffentlichen Key kopieren:
        ```powershell
        cat ~/.ssh/id_ed25519.pub  # oder cat ~/.ssh/gitlab_key.pub
        ```
        Gesamten Output des Command-Lines kopieren. 
   2. Key in GitLab einfügen: 
      1. Gehe zu deinem [Gitlab-Account](https://gitlab.com/)->Auf deinem Profil Icon klicken->[Preferences](https://gitlab.com/-/profile/preferences)->[SSH-Keys](https://gitlab.com/-/user_settings/ssh_keys).
      2. Füge den Key, den du kopiert hast, ein und vergeb ihm einen Titel (z.B. "Windwos 11 SSH-Key Loccoz System AG).
      3. Klicke auf __Add key__.
7. Verbindung testen
    ```powershell
    ssh -T git@gitlab.com
    ```
    Falls keine Erfolgsmeldung:
        - Error kopieren
        - Unter [Troubleshooting](#trouble) einfügen
    Erfolgsmeldung:
    ```powershell
    Welcome to GitLab, @DeinUsername!
    ```
8. (Optional) Mehrere SSH-Keys verwalten
   Falls du verschiedene Keys für GitLab, GitHub, etc. nutzt:
   1. `~/.ssh/config` erstellen/bearbeiten:
        `notepad ~/.ssh/config`
   2. Key-Zuordnung definieren (Beispiel, letze Zeile mit {data-source-line="108"} __ignorieren__)
        ```
        # GitLab Host gitlab.com
            HostName gitlab.com
            User git
            IdentityFile ~/.ssh/gitlab_key
            IdentitiesOnly yes

        # GitHub
        Host github.com
            HostName github.com
            User git
            IdentityFile ~/.ssh/github_key
            IdentitiesOnly yes 
        ```

    3. Verbindung testen `ssh -T git@gitlab.com  # Test für GitLab`

__Bitte alte Keys, `id_rsa`, durch einen modernen Ed25519-Key ersetzen (siehe Schritt 3)__

Source: [Use SSH keys to communicate with GitLab](https://docs.gitlab.com/user/ssh/)

## Git in Powershell Aufsetzen

1. Git herunterladen
   - Gehe zur offiziellen [Git-Website](https://git-scm.com/downloads/win)
   - In den meisten Fällen kannst du `Git for Windows/x64 Setup` herunterladen.
2. Installer ausführen
   - Doppelklicke auf die heruntergeladene .exe-Datei
   - Folge den Installationsanweisungen (die Standardoptionen sind in der Regel in Ordnung)
3. Wichtige Optionen während der Installation
   - Wähle "Git from the command line and also from 3rd-party software" (dies integriert Git in PowerShell)
   - Wähle "Use Windows' default console window"
   - Aktiviere "Enable file system caching" und "Enable Git Credential Manager"
4. Installation überprüfen
   - Öffne Powershell (als Admin oder normal)
   - Führe folgenden Befehl aus:
    ```powershell
    git --version
    ```
    Du solltest die installierte Git-Version sehen (z.B `git version 2.40.1`)
5. Git konfigurieren
   - Benutzerinformationen einrichten (erforderlich für Commits). Für user name gibt es für LoccoZ eine Konvention. Beispiel mit Masiar Etemadi:
   ```powershell
    git config --global user.name "maet-loccoz" # Die ersten zwei Buchstaben des Vor- und Nachnamens kombinieren
    git config --global user.email "masiar@etemadi.com"
   ```
   - Falls man seine Konfigurationen überprüfen möchte:
    ```powershell
    git config --list
    ```
6. LoccoZ-Organisation Repository klonen
    ```powershell
    C:\Users\$USERNAME$> cd ~/path/to/desired/destination
    C:\Users\$USERNAME$> git clone git@gitlab.com:loccoz-system-ag/loccoz-organization.git
    ```
    Nun hast du den Remote Repository lokal auf dein Rechner. Hier ist nun ein guter Punkt sich aktiv mit Git auseinanderzusetzen. Ich weise auf das OneNote von I0045-I-SYSTEM hin, wo unter dem Abschnitt "Git" verschiedenste Tutorials zu Git aufgelistet sind. Nebst den Tutorials findest du auch Cheatsheets und offizielle Dokumenationen zu Git.

## Commit Regeln und Konventionen
Für Software-Entwicklungen empfehle ich stark sich an diese [Commit-Konvention](https://www.conventionalcommits.org/en/v1.0.0/) zu halten. Dies standardisiert die Commit-Historie, macht es für andere Mitarbeiter einfacher den Verlauf des Codes zu verstehen und mögliche Bugs schneller zu erkennen.

Eine weitere Empfehlung für Commits ist es, in kleineren Intervallen und nach kleineren Änderungen Commit Nachrichten zu erfassen, statt nach vielen Veränderungen eine Commit Nachricht zu erfassen. Man kann das analog wie den Spielfortschritt in einem Game betrachten:
Man versucht bei gewisssen, schwierigen Spielen, falls diese Option besteht, in kurzen Intervallen den Spielstand zu speichern. Dies gibt dem Spieler eine Sicherheit, falls sein Avatar während dem Spielverlauf stirbt, seine Stadt in Schutt und Asche liegt oder sogar schlimmer seine Konsole plötzlich den Geist aufgibt.


## Troubleshooting
__Bitte alle Probleme, die du bei der Installation erhältst, bitte hier einfügen.__ 
1. Problem 1
2. Problem 2
3. ...


# Getting started

Dieses Tutorial ist eine *do it yourself*-Anleitung: Hier baut ihr euch lokal eine Struktur aus drei Repositorien auf, die die übliche Arbeitssituation mit Online-Repositorien simuliert. Wenn ihr an diesem Punkt euch fragt, wovon ich überhaupt rede: Keine Sorge, eine aufeinander aufbauende Erläuterung von Git-Begriffen findet ihr im Abschnitt [Was ist ...?](was-ist.md). Aber zu Beginn: Folgt einfach erst einmal der Anleitung Schritt für Schritt und erstellt die Experimentalumgebung.

- Installiert euch [Git für euer Betriebssystem](https://git-scm.com/downloads) auf eurem Rechner.
- Öffnet ein Terminal mit Kommandozeile (*Git Bash* in Windows) und testet die Git-Installation: `git --version` sollte eine Versionsnummer ausgeben.
- Erzeugt einen Tutorial-Ordner irgendwo auf eurem System und öffnet diesen Ordner im Terminal.
- Initialisiert dort ein blankes (englisch: bare) Repository: `git init --bare an-online-repo.git`.
- Erzeugt zwei weitere Ordner im Tutorial-Ordner: `mkdir "userA"` und `mkdir "userB"`.
- Wechselt im Terminal in einen der User-Ordner und klont das "Online"-Repo dort hinein: `git clone ../an-online-repo.git`.
- Wiederholt den vorigen Schritt im anderen User-Ordner.
- Wechselt im Terminal in einen der durch Klonen erzeugten Repo-Ordner `/an-online-repo`.
- Erzeugt dort eine neue Datei: `echo "# Dies ist eine Readme-Datei" > readme.md`.
- Erzeugt einen ersten Commit: `git add readme.md` und `git commit -m "erster Commit"`.
- Pusht den Commit (und damit auch den Master-Branch) in das "Online"-Repo: `git push`.
- Wechselt im Terminal in den anderen durch Klonen erzeugten Repo-Ordner `/an-online-repo`.
- Pullt dort die neusten Änderungen, also den Master-Branch mit dem ersten Commit: `git pull`.
- Finaler Check: In beiden Ordnern `UserA/an-online-repo/` und `UserB/an-online-repo/` sollte nun die Datei `readme.md` liegen.

Glückwunsch! Jetzt habt ihr lokal die Situation hergestellt, die beim kollaborativen Arbeiten mit Online-Repositorien üblich ist: Es gibt ein blankes Online-Repositorium, auf das mehrere User mit lokalen Repositorien zugreifen.

<img src="../images/grafiken_repositories-01.png" alt="Remote- und lokale Repositories" title="Remote- und lokale Repositories" width="300"/>

Um die User A und B in der History der Repos zukünftig besser zu unterscheiden, wechselt nun im Terminal noch nacheinander in die geklonten Repos und gebt Namen und E-Mail-Adressen der User ein. Diese Einstellungen gelten nur jeweils für das Repository:

```
cd Tutorial-Ordner/UserA/an-online-repo
git config --local user.name "UserA"
git config --local user.email "UserA@example.com"

cd Tutorial-Ordner/UserB/an-online-repo
git config --local user.name "UserB"
git config --local user.email "UserB@example.com"
```

Von hier ab könnt ihr selbst in der Testumgebung experimentieren.

Als Hilfestellung und Inspiration findet ihr im Folgenden:

- einen [Was ist ...?](was-ist.md)-Abschnitt, der für Einsteiger und Fortgeschrittene die Git-Begriffe und -Funktionsweise erklärt,
- eine [Liste mit Cheat Sheets und der offiziellen Git-Dokumentation](cheat-sheets.md), mit der ihr git-Befehle nachschlagen könnt,
- und eine [Liste mit Experimentalanleitungen](experimente/1-ein-einfacher-kollaborativer-git-workflow.md) zu verschiedenen Themen.

Gute Einstiegspunkte sind der [Was ist ...?](was-ist.md)-Abschnitt und, falls ihr mit den Git-Grundbegriffen schon etwas anfangen könnt, [Experiment #1](experimente/1-ein-einfacher-kollaborativer-git-workflow.md).

Have fun!

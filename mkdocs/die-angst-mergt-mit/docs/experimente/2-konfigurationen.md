# 2. Konfigurationen
## Generelles und Einstellungen anschauen
Git hält auf verschiedenen Ebenen Konfigurationen in Form von Key-Value-Paaren (z.B. "user.name": "ein Username")bereit: lokale, globale und systemweite. Erstere betreffen nur jeweils ein Repository und sind entsprechend auch darin gespeichert. Was dort alles eingestellt ist, könnt ihr euch im Vergleich zwischen einem der User-Repos und unserem "Online"-Repo anschauen: Geht im Terminal jeweils in eines der Repos und tippt `git config --local --list`. Praktischer Hinweis: `an-online-repo.git` könnt ihr im Terminal wie einen Ordner betreten.

Zu sehen ist hier, dass der Key `core.bare` im "Online"-Repo den Wert `true` hat und dass entsprechend nur die User-Repos die Einstellungen `remote.origin.url`, `remote.origin.fetch`, `branch.master.remote` und `branch.master.merge` besitzen, da das Online-Repo keinen Verweis auf ein Remote-Repo besitzt.

Globale Konfigurationen betreffen User-relevante Konfigurationen und systemweite Einstellungen betreffen alle übrigen Einstellungen. In der Regel sind das unterschiedliche Einstellungen. Im Falle der User-Daten (`user.name` und `user.email`) aber können diese Angaben zugleich global und lokal existieren: Die lokalen Angaben, so sie in einem Repo gesetzt sind, überschreiben dann die globalen. Wenn ihr aber einen bestimmten Usernamen und eine bestimmte E-Mail-Adresse für eure Arbeit an allen Repos auf eurem System benutzen wollt, definiert die Werte einmal global. Dann müsst ihr sie nicht in jedem Repo neu definieren.

Um euch gezielt globale und systemweite Konfigurationen anzugucken, gebt im Terminal (egal in welchem Ordner) ein: `git config --global --list` und `git config --system --list`. Um euch alle Konfigurationen aller Ebenen in einer Liste anzuschauen, tippt `git config --list`. Da hier die Geltungsebene für die einzelne Einstellung nicht mehr zu erkennen ist, könnt ihr euch zusätzlich auch den Pfad zur entsprechenden Konfigurationsdatei mitanzeigen lassen: `git config --list --show-origin`.

## Einstellungen ändern
Mit dem Wissen, wo die Konfigurationsdateien zu finden sind, könnt ihr sie natürlich direkt in einem Editor bearbeiten. Um aber eine neue Einstellung im Terminal zu setzen, etwa auf globaler Ebene euren Usernamen und eure E-Mail-Adresse, tippt:

```
git config --global user.name "Maria Mustermann"
git config --global user.email "mm@example.com"
```

Wollt ihr einen Eintrag, etwa für die E-Mail-Adresse, nicht nur überschreiben, sondern löschen, nutzt: `git config --global --unset user.email`.

## Notwendige Einstellungen
Zwei Einstellungen sollten in jedem Fall gesetzt und/oder überprüft werden:

- `user.name` und `user.email` (lokal oder global): Ohne diese Werte ist es nicht möglich, einen Commit zu machen.
- `core.autocrlf` (system): Diese Werte sollte bei Windows-System auf `true` und bei allen anderen Betriebssystemen auf `input` gesetzt sein. Da Windows-System das Ende einer Zeile in Textdateien anders codieren als Linux- und Mac-System, muss beim kollaborativen Arbeiten gewährleistet sein, dass a\) in allen Textdateien die gleiche Zeilenend-Codierung verwendet wird und b\) dass alle User lokal in ihrem Working Tree die Textdateien für ihr Betriebssystem korrekt angezeigt bekommen. Ansonsten kann es dazu kommen, dass Änderungen in der Zeilenend-Codierung von Git als Änderungen in der Datei erkannt und gespeichert werden. Damit wird die History mit belanglosen Änderungen geflutet.

## Übersicht über alle Einstellungen

<img src="../../images/under-construction_symbol.png" alt="Under Construction" width="30" /> to be added

<!-- nützlich: git config --global init.defaultBranch trunk; siehe https://stackoverflow.com/questions/42871542/how-can-i-create-a-git-repository-with-the-default-branch-name-other-than-maste -->

[Zurück zum Anfang](#2-konfigurationen)

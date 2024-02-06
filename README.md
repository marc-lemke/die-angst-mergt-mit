# Die Angst mergt mit - ein git-Tutorial

Dieses Repository enthält den aktuellen HTML-Build des gleichnamigen MkDocs-Projekt, das sich im Ordner `/mkdocs` befindet. Sinn dieser Konstruktion ist, dass das Tutorial über **github pages** unter der Adresse [https://marc-lemke.github.io/die-angst-mergt-mit/](https://marc-lemke.github.io/die-angst-mergt-mit/) zugänglich ist.

## Requirements 

Zur Arbeit mit dem Repository werden Python, `pip` und die folgenden Pakete benötigt:
- `mkdocs` (1.5.X)
- `mkdocs-material` (9.X)
- `mkdocs-material-extensions` (1.X)

## Abläufe

Editieren des MkDocs-Projekt:
- `cd mkdocs/die-angst-mergt-mit`
- `mkdocs serve`
- Es startet ein lokaler Server, der die Änderungen in den Markdown-Dateien und in der `.yaml`-Konfigurationsdatei nach dem Speichern von Änderungen direkt anzeigt.

Aktualisieren des HTML-Builds:
- `cd mkdocs/die-angst-mergt-mit`
- `mkdocs build`
- Es wird ein HTML-Build im neu erzeugten Unterorder `/site` angelegt. Ersetzt die Dateien im root-Verzeichnis des Repos mit den Dateien aus dem `/site`-Verzeichnis und löscht letzteres anschließend, damit der Build nicht unnötigerweise zwei mal im Repo abgespeichert wird.

## Dokumentationen

- [MkDocs](https://www.mkdocs.org/)
- [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/)
- [MkDocs Material Extensions](https://pypi.org/project/mkdocs-material-extensions/)

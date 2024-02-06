# 1. Ein einfacher kollaborativer Git-Workflow

Willkommen bei **Experiment #1**! Hier lernst du konkrete Schritte und Routinen kennen, die üblicherweise in kollaborativen Projekten mit Git anfallen. Das Setup entspricht der Konstellation, die du bereits auf deinem Rechner lokal erzeugt hast: Es gibt ein Online-Repo, auf das mehrere User des Projekts mit lokalen Klonen zugreifen.

Die konkreten Arbeitsweisen mit Git hängen natürlich auch immer vom Arbeitsmaterial und den Zielen des Projekts ab. Im Folgenden findest du daher **zwei exemplarische Projekt-Szenarien**:

- A: den Aufbau eines Text-Korpus und
- B: die Entwicklung einer Website.

In den Szenarien werden Git-Befehle im Terminal verwendet. (Windows-User sollten hier *Git Bash* nutzen, da die Dateipfade im Tutorial durchweg im UNIX-Format mit `/` geschrieben sind.) Am Ende dieses Experiments stelle ich aber auch noch **Tools mit grafischen Benutzeroberflächen** vor, die in der täglichen Arbeit mit Git verwendet werden können: das [Git Client Add-On für den Oxygen XML Editor](#git-client-add-on-fur-oxygen-xml-editor), den Code-Editor [VSCode mit der *Git Graph*-Extension](#visual-studio-code-vscode-mit-git-graph-extension) und die Software [GitHub Desktop](#github-desktop).

Sollten hier Begriffe fallen, die euch unklar sind, schaut einmal in der [Was ist ... ?](../was-ist.md)-Abteilung nach.

## Szenario A: Text-Korpus

Das Projekt-Team will ein Text-Korpus aufbauen und veröffentlichen, es fallen mehrere Aufgaben an: Textquellen ausfindig machen, Texte auswählen, ein Dateiformat und Textschema für das Korpus definieren, Texte in das gewählte Schema bringen und die Arbeit sowie das Korpus dokumentieren.

Für das Repo bedeutet das, dass es verschiedene Arten von Dateien gibt:

- Texte im Quellformat
- Texte im Projektformat
- Transformationsskripte
- Textschema
- Dokumentationsdateien

UserA und UserB entscheiden sich dafür, die Aufgaben aufzuteilen: UserA sucht Texte im Quellformat, lädt sie hoch und dokumentiert das Projekt; UserB schreibt Transformationsskripte und erzeugt die Texte im Projektformat. Beide zusammen arbeiten am Textschema und überprüfen die transformierten Texte auf Fehler.

| User | Aufgabe | Ordner/Dateien |
|--------------|-----------|------------|
| UserA | Texte im Quellformat suchen und hochladen | /sources |
|  | Dokumentation anlegen und pflegen | /docs + readme.md |
| UserB | Transformationsskripte schreiben | /scripts |
|  | Texte im Projektformat erzeugen | /corpus |
| beide | Textschema definieren | /scheme |
|  | Korrekturlesen der Texte | /corpus |

Wenn die Projektgruppe sehr klein ist und die Aufgaben so verteilt sind, dass es kaum dazu kommt, dass mehrere User die selben Dateien editieren, ist es kein Problem, wenn alle Beteiligten auf dem selben Branch arbeiten: Es wird selten zu Merge-Konflikten kommen. Das bedeutet nicht, das ein Merge-Konflikt ein unbedingt zu vermeidender Ausnahmezustand ist. Im kollaborativen Arbeiten sind Konflikte im Gegenteil eine erwartbare, normale Situation. Wir werden sehen: Git bietet genau dafür den Usern Funktionen, um damit umzugehen. Aber es beschleunigt andererseits natürlich die Projekt-Arbeit auf lange Sicht, wenn die Anzahl der Situationen, in denen Merge-Konflikte zu lösen sind, gering ist.

Da das Projekt-Team plant, das Korpus wie eine Software versioniert zu veröffentlichen, soll das Repo zwei Branches haben: einen Arbeitsbranch und einen, auf dem nur die Releases hergestellt werden. Unabhängig der Gepflogenheit, den `master` für fertige Releases zu nutzen und nebenbei einen `develop`-Branch für die tägliche Arbeit zu haben, werden sie auf dem `master` arbeiten und die fertigen Releases auf dem `releases`-Branch mit Tags markieren: Der `master` ist in Git kein besonderer Branch, sondern einfach nur eine Benennungskonvention für den ersten Branch.

Die Projekt-Arbeit beginnt ...

----------

<img src="../../images/attention_symbol.png" alt="Attention" width="25"/> *Jetzt im Terminal in das Repo von UserA wechseln*

**UserA** hat einen Quelltext gefunden und will ihn zusammen mit einer angefangenen Liste der für das Projekt ausgewählten Texte für die Dokumentation hochladen. Davor aber folgt er einer täglichen Routine, auf die sich das Team geeignet hat:

```
## prüfen, auf welchem Branch man sich lokal befindet
git status

## lokalen Branch auf master wechseln, falls nötig
git checkout master

## die neuesten Daten vom Remote-Branch herunterladen
## und direkt in den lokalen Branch einfügen
git pull
```

Er legt nun die Ordner `/sources` und `/docs` sowie die Dateien `text001.txt` und `textList.csv` an. Wir benutzen Dummy-Dateien. Du kannst sie im Explorer oder im Terminal erzeugen:

```
mkdir "sources"
cd sources
echo "Lorem Ipsum" > text001.txt
cd ..

mkdir "docs"
cd docs
echo "Text,Quelldatei,Quelle" > textList.csv
echo "Text001,text001.txt,Projekt Gutenberg" >> textList.csv
cd ..
```

UserA will die Änderungen nun stagen, commiten und in das Remote-Repo hochladen. Dabei entscheidet er sich dafür, zwei Commits daraus zu machen, da die vorgenommenen Änderungen zwei unterschiedliche Aufgabenbereiche betreffen: Quelltextablage und Dokumentation. Das hat in der Zukunft einen möglichen Vorteil: Entscheidet man sich später dafür, Text001 doch nicht in das Korpus aufzunehmen, kann man dann gezielt den einen Commit rückgängig machen, mit dem die txt-Datei hinzugefügt wurde, ohne zugleich auch die `textList.csv` mit entfernen zu müssen.

Übrigens: Mit "einen Commit rückgängig machen" ist nicht löschen gemeint, sondern das Erzeugen eines neuen Commits, der die Änderungen des alten Commits mittels `git revert` rückgängig macht. Mehr dazu findet ihr im [Experiment #7](../7-fehler-rueckgaengig-machen/).

```
## prüfen, welche Veränderungen im Working Tree erkannt
## worden sind: "git ls-files --others" zeigt alle bisher
## ungetrackten Dateien an; tippt einmal "git ls-files -h",
## um weitere Flags kennenzulernen
git status
git ls-files --others

## den Text stagen, um ihn zum Commit vorzubereiten
git add sources/text001.txt

## überprüfen, welche Dateien für den folgenden Commit
## gestaged sind: "git diff --name-only --cached" zeigt
## die Namen der gestagten Dateien an, die im Vergleich
## zum letzten Commit geändert wurden
git status
git diff --name-only --cached

## committen: die Nachricht nach der "-m"-Flag
## kann weggelassen werden; dann öffnet sich
## entsprechend der Texteditor, auf den in der
## Konfiguration "core.editor" verwiesen wird,
## um die Commit-Nachricht einzugeben; die
## Konfiguration kann mittels
## "git config core.editor" angezeigt werden
git commit -m "Text001-Quelltext hinzugefügt"

## die Textliste stagen
git add docs/textList.csv

## den zweiten Commit erzeugen
git commit -m "Textliste hinzugefügt"

## die Commits in das Remote-Repo pushen
git push
```

----------

<img src="../../images/attention_symbol.png" alt="Attention" width="25"/> *Jetzt im Terminal in das Repo von UserB wechseln.*

**UserB** beginnt danach einen Entwurf für das Textschema zu entwerfen und lädt das Dokument hoch. Wie gehabt, folgt auch er zuvor der vereinbarten Routine:

```
## prüfen, auf welchem Branch man sich lokal befindet
git status

## Hinweis: Da an dieser Stelle UserB noch keine Updates
## vom Remote-Repo geholt hat, steht in der Status-Meldung
## "Your branch is up to date with 'origin/master'".
## Die Meldung aktualisiert sich erst nach einem fetch.
## Verwendet man zum Updaten dagegen pull, werden die
## Änderungen direkt in den lokalen Branch gemergt,
## was bewirkt, dass eine anschließende status-Anfrage
## wiederum ergibt, dass der lokale Branch uptodate ist,
## was er dann ja auch tatsächlich ist. Zusammengefasst:
## Der status-Befehl macht keine eigene Abfrage, sondern
## vergleicht nur den lokalen Branch mit der zuletzt
## gefetchten Version des Remote-Branches.

## falls nötig: lokalen Branch auf master wechseln
git checkout master

## die neuesten Daten vom Remote-Branch herunterladen
## und direkt in den lokalen Branch einfügen: UserB
## sieht nun die Text-Datei und Text-Liste,
## die UserA gepusht hat
git pull
```

Dummy-Datei anlegen:

```
mkdir "scheme"
cd scheme
echo "a scheme file" > project.odd
cd ..
```

Stagen, committen und pushen:

```
## prüfen, welche Veränderungen im Working Tree erkannt
## worden sind
git status
git ls-files --others

## den Text stagen, um ihn zum Commit vorzubereiten
git add scheme/project.odd

## überprüfen, welche Dateien für den folgenden Commit
## gestaged sind
git status
git diff --name-only --cached

## committen
git commit -m "erste Version des Schemas hinzugefügt"

## die Commits in das Remote-Repo pushen
git push
```

----------

<img src="../../images/attention_symbol.png" alt="Attention" width="25"/> *Jetzt im Terminal wieder in das Repo von UserA wechseln.*

**UserA** möchte nun weitere Quelltexte hinzufügen, `textList.csv` aktualisieren und eine erste Version der `readme.md` des Repos hochladen. Diesmal aber vergisst er die vereinbarte Routine und pullt nicht den aktuellen Stand des Remote-Repos. Mal schauen, was passiert.

Zunächst legen wir wieder die Dummy-Dateien an und aktualisieren `textList.csv` sowie `readme.md`. Im Code entferne ich hier die alte `readme.md` und füge eine neue ein. Für Git ist dies jedoch eine Aktualisierung, da der Dateiname der gleiche ist. Ihr könnt die Änderungen wie gehabt im Datei-Explorer oder im Terminal durchführen.

```
cd sources
echo "Lorem Ipsum zum Zweiten" > text002.txt
echo "Lorem Ipsum zum Dritten" > text003.txt
cd ..

cd docs
echo "Text002,text002.txt,Projekt Gutenberg" >> textList.csv
echo "Text003,text003.txt,DTA" >> textList.csv
cd ..

rm -f readme.md
echo "# Projekt-Korpus" > readme.md
```

Wiederum entscheidet UserA nun, drei Commits aus dem Änderungen zu machen: einen für die Quelltexte, einen für die Aktualisierung der `textList.csv` und einen für die Aktualisierung der `readme.md`:

```
## schauen, welche Veränderungen im Working Tree erkannt
## worden sind
git status
git ls-files --others
git ls-files --modified

## die Texte stagen, alle Änderungen im Order "/sources"
git add sources

## überprüfen, welche Dateien für den folgenden Commit
## gestaged sind
git status
git diff --name-only --cached

## committen
git commit -m "Quelltexte 2 und 3 hinzugefügt"

## die aktualisierte Textliste stagen, auch als Abkürzung
## den Ordner "/docs", da wir alle geänderten Dateien
## in "/docs" stagen wollen, auch wenn es nur eine ist
git add docs

## überprüfen, welche Dateien für den folgenden Commit
## gestaged sind
git status
git diff --name-only --cached

## committen
git commit -m "Texte 2 und 3 zur Textliste hinzugefügt"

## die aktualisierte readme.md stagen
git add readme.md

## überprüfen, welche Dateien für den folgenden Commit
## gestaged sind
git status
git diff --name-only --cached

## committen
git commit -m "Entwurf für ernsthafte Repo-Readme"
```

Nun zum interessanten Teil: `git status` würde an diesem Punkt die Nachricht `Your branch is ahead of 'origin/master' by 3 commits` ausgeben. Jedoch hat UserA bekanntlich vergessen, sich den aktuellen Stand vom Remote-Repo zu holen. Er versucht jetzt, seine Änderungen zu pushen, und bekommt eine Meldung:

```
## push
git push

 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'D:/repositories/git_tutorial/UserA/..\an-online-repo.git'
hint: Updates were rejected because the remote contains work that you do not
hint: have locally. This is usually caused by another repository pushing to
hint: the same ref. If you want to integrate the remote changes, use
hint: 'git pull' before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

Mit `git push` werden die Wissensstände der Repos (lokal und Remote) miteinander verglichen: Dadurch wurde nun erkannt, dass inzwischen UserB Commits zum Master-Branch des Remote-Repos hinzugefügt hat. Vollautomatisch wird die Situation von Git nicht aufgelöst, da die Kontrolle in diesen Fällen beim User liegen soll.

Die Lösung für das Problem ist, mit `git pull` sich den neuesten Stand zu ziehen und lokal diesen Stand mit dem lokalen Stand zu mergen. Das heißt, dass lokal ein neuer Commit erzeugt wird, der den Stand des letzten eigenen lokalen Commits mit dem des letzten Commits aus dem Remote-Repo auf dem `master`-Branch verbindet. Dieses Vorgehen ist nötig, um zu prüfen, ob die Änderungen von UserA und UserB miteinander kompatibel sind. In diesem Fall ist dieser Merge unproblematisch, da beide User an unterschiedlichen Dateien gearbeiten haben. Versuchen wir es:

```
## den neuesten Stand des Remote-Master-Branches ziehen
## und versuchen, mit dem lokalen Branch zu mergen
git pull

## der Vorgang wird hier eventuell (falls die Einstellung
## nicht schon gemacht worden ist) abgebrochen und wir
## bekommen als Antwort die Aufforderung, zu entscheiden,
## mit welcher Methode Git divergierende Branches
## (zwischen dem Remote- und dem lokalen Repo) auf
## einen Stand bringen soll
hint: You have divergent branches and need to specify how to reconcile them.
hint: You can do so by running one of the following commands sometime before
hint: your next pull:
hint:
hint:   git config pull.rebase false  # merge
hint:   git config pull.rebase true   # rebase
hint:   git config pull.ff only       # fast-forward only
hint:
hint: You can replace "git config" with "git config --global" to set a default
hint: preference for all repositories. You can also pass --rebase, --no-rebase,
hint: or --ff-only on the command line to override the configured default per
hint: invocation.
fatal: Need to specify how to reconcile divergent branches.

## An der Stelle kann ein Texteditor automatisch geöffnet
## worden sein, um die Commit-Nachricht für den neuen
## Merge-Commit zu editieren: Ihr könnt die Nachricht editieren.
## Schließt am Ende den Editor, um den pull-Vorgang fortzusetzen.
```

Falls wir die Methode festlegen müssen, die `git pull` zum Verbinden unterschiedlicher Branch-Zustände nutzen soll, entscheiden wir uns für `merge`. `rebase` ist ein Verfahren, dass im Gegensatz zu `merge` keinen neuen Commit erzeugt, sondern lokal die Commits von UserB in die eigene History so einfügt, dass es so aussieht, als hätte UserA vor seinen eigenen Commits sich doch den aktuellen Stand des Remote-Repos via `git pull` geholt. `rebase` verändert also die History des Repos. Für diesen lokalen Anwendungsfall hier ist das kein Problem und es können `merge` und `rebase` gleichermaßen genutzt werden. `rebase` ist dagegen problematisch, wenn es direkt im Remote-Repo angewendet wird und mehrere User mit dem betroffenen Branch arbeiten: Ihre lokalen und Remote-Branches unterscheiden sich dann plötzlich in ihrer History und es hagelt Fehlermeldungen. Zuletzt noch zur dritten genannten Option `fast-forward only`: Sie würde unser Problem nicht lösen, da *Fast-Forward* genau den optimalen Fall meint, dass mein lokaler Branch keine dem Remote unbekannten Commits besitzt.

Falls nötig, entscheiden wir uns also für `merge`, stellen das in der globalen Konfiguration ein und versuchen erneut, mit `git pull` die unterschiedlichen Commits von UserA und UserB lokal in einem Commit auf `master` zu verbinden. Wir schauen uns das Ergebnis im Log grafisch an und pushen das Ergebnis.

```
## falls nötig: pull-Methode einstellen, auf globalem Level,
# da merge in allen Fällen eine sichere Methode ist
git config --global pull.rebase false

## falls nötig: pull-Versuch wiederholen, es sollte
## nun automatisch ein neuer lokaler Commit erzeugt werden
git pull

## die Commit-Liste mit grafischer Repräsentation der
## zweitweise unterschiedlichen Branch-Zustände ansehen
git log --graph

## Wir können hier den üblichen Kommentar eines Commits
## sehen, der von Git mit pull automatisch erzeugt
## worden ist: "Merge branch 'master' of <REPO>".
## Und wir können sehen, dass der Commit von UserB nun
## auch als Verweis in unser lokalen Commit-History
## auftaucht. Die grafische Darstellung zeigt den
## kurzzeitig differierenden Zustand des Master-Branches
## zwischen UserA und UserB: UserB kannte zum Zeitpunkt
## seines Schema-Commits die drei Commits von UserA
## noch nicht. Die Linien zeigen hier also den Stand
## des gleichen Branches bei unterschiedlichen Usern.

## "git log" kann eventuell eine längere Liste an
## Commits anzeigen. Im Terminal wird diese Liste
## womöglich in Abschnitten angezeigt. Mit "Leerzeichen"
## könnt ihr zum nächsten Abschnitt wechseln und
## mit "Q" die Listenansicht verlassen.

## Repo-Status anschauen: Unser lokaler Branch ist
## nun 4 Commits weiter als der Remote-Branch - mit
## den drei eigenen Commits und dem, der durch den
## pull-Befehl erzeugt worden ist
git status

## pushen
git push
```

Voilá, UserA hat die unterschiedlichen Stände des Master-Branches angeglichen. Vor dem Committen den neuesten Stand zu pullen, hätte die Situation zwar verhindert. Aber Git als kollaboratives Versionierungstool bietet Möglichkeiten, mit solchen unterschiedlichen Zuständen von lokalem und Remote-Branch umzugehen. Dafür ist es gedacht. Das sehen wir jetzt auch im letzten Abschnitt dieses Szenarios, in dem Konflikte in ein und derselben Datei auftreten.

----------

<img src="../../images/attention_symbol.png" alt="Attention" width="25"/> *Im Terminal in das Repo von UserB wechseln.*

**UserB** seinerseits vergisst nun auch, sich zu Beginn seiner Arbeit am Repo den neuesten Stand vom Remote herunterzuladen. Entgegen der abgesprochenen Aufteilung der Aufgaben möchte er zudem spontan für die `readme.md` des Repos einen ersten Vorschlag zum Projekt beisteuern.

```
## Dummy-Datei mit anderer Überschrift anlegen,
## als UserA in seiner Version verwendet hat
rm -f readme.md
echo "# Korpus" > readme.md
```

UserB staged die Änderung, erzeugt einen Commit und versucht ihn zu pushen.

```
## readme.md stagen
git add readme.md

## überprüfen, welche Dateien für den folgenden Commit
## gestaged sind
git status
git diff --name-only --cached

## committen
git commit -m "Projekt-Readme-Entwurf"

## pushen
git push
```

UserB wird - wie wir es bereits bei UserA gesehen haben - auf einen Konflikt hingewiesen und gebeten, den neuesten Stand des Remote-Masters zu pullen. Eventuell wird auch er dabei aufgefordert zu entscheiden, mit welcher Methode `git pull` verschiedene Branch-Zustände zwischen lokalem und Remote-Branch vereinen soll. UserB entscheidet sich in diesem Fall ebenso wie UserA für `merge`.

```
## falls nötig: pull-Methode einstellen
git config --global pull.rebase false

## pull-Versuch
git pull
```

Im Gegensatz zur Situation von UserA kann Git hier die Commits von lokalem und Remote-Branch aber nicht einfach verbinden, denn UserA und UserB haben jeweils die erste Zeile der `readme.md` verändert. Git kann somit nicht entscheiden, welche Version die gewünschte ist, und übergibt die Entscheidung folgerichtig dem User:

```
Auto-merging readme.md
CONFLICT (content): Merge conflict in readme.md
Automatic merge failed; fix conflicts and then commit the result.
```

Um den Konflikt manuell zu lösen, muss UserB die `readme.md` nun in seinem Working Tree mit einem Text- oder Code-Editor öffnen. Dort fällt auf, dass Git den Inhalt der Datei verändert hat:

```
<<<<<<< HEAD
# Korpus
=======
# Projekt-Korpus
>>>>>>> 54a0706feaab2da3789b2693320a7fb5fea5afcb
```

Die Schreibweise markiert hier, wo der Konflikt sich genau befindet und welche Version der betroffenen Zeile im Dokument von welchem Repo kommt: `<<<<<<<` markiert den Beginn des Konflikts. Die folgenden Zeilen bis `=======` sind die Version des sogenannten Basis- oder Head-Branches, also des Branches, der das Ziel der Merge-Prozedur ist - in diesem Fall ist das die Version von UserB auf seinem lokalen `master`-Branch. Zwischen `=======` und `>>>>>>>` folgt die Version des Branches, der in den Basis-Branch gemergt werden soll: die Version von UserA aus dem Remote-`master`.

Um den Konflikt zu lösen, müssen die drei Zeilen mit den von Git gesetzten Steuerzeichen komplett gelöscht werden. UserB kann sich für eine der Varianten entscheiden oder die Zeile anders schreiben. Am Ende jedenfalls muss die Datei gespeichert, neu gestaged und committet werden. Wenn das für alle Dateien mit manuell zu lösenden Merge-Konflikten erledigt ist, ist der pull-Vorgang abgeschlossen. Die Änderungen können dann sofort gepusht werden.

```
## status anzeigen: readme.md ist hier noch als
## Konflikt-Datei markiert
git status

## stagen nach dem manuellen Lösen des Konflikts
git add readme.md

## commit
git commit -m "Merge-Konflikt gelöst: Überschrift in Readme.md"

## zum Testen: einmal pullen - der pull-Vorgang ist
## jedoch schon abgeschlossen
git pull

## pushen
git push
```

Damit endet dieses kleine Szenario: Neben den täglichen Git-Routinen und den häufig verwendeten Git-Befehlen war vor allem zu sehen, wie man bei der kollaborativen Arbeit auf einem Branch mit Merge-Konflikten umgeht.

Im zweiten Szenario nun ist die Arbeitsweise eine etwas andere: Hier arbeiten die Beteiligten auf unterschiedlichen Branches und müssen diese von Zeit und Zeit wieder auf einen Stand bringen. Auch wie man einen Release auf dem Release-Branch herstellt und einen Versions-Tag zuweist, werden wir in diesem Szenario sehen.

## Szenario B: Website

Das zweite Projekt-Team entwickelt eine Website. Auch sie richten sich einen `releases`-Branch ein, um die Commits mit den fertigen Versionen dort zu speichern, und auch hier findet die Entwicklung auf dem `master` statt. Anders als das Korpus-Team entwickeln aber die Projekt-Mitglieder jeweils einzelne Features (wie eine Such-Funktion oder das Hauptmenü oder eine Seite auf der Website) auf speziell dafür temporär angelegten Branches. Sobald ein Feature fertig ist, wird der Feature-Branch in den `master` gemergt.

Dieses Vorgehen ist bei diesem Projekt sinnvoll, denn auf diese Weise können zwischenzeitlich, auch wenn ein Feature noch nicht ganz fertig entwickelt ist, die anderen Projekt-Mitglieder sich die Website schon einmal mit dem neuen Feature anschauen: Dafür wechseln sie einfach kurz auf den entsprechenden Feature-Branch, um sich einen Eindruck vom zukünftigen Zustand der Website in ihrem Working Tree zu verschaffen.

Falls ihr schon Szenario A durchgespielt habt, gehen wir der Einfachheit halber davon aus, dass das Korpus-Team auch eine Website entwickelt, um die Arbeit im Web zu präsentieren. In der Praxis würde man dafür ein zweites Repo anlegen, um Website und Korpus in der Versionierung voneinander zu trennen.

----------

<img src="../../images/attention_symbol.png" alt="Attention" width="25"/> *Im Terminal in das Repo von UserA wechseln.*

**UserA** führt die täglichen Routinen aus, legt in seinem lokalen Repo einen `/website`-Ordner an, fügt eine `index.html` mit einem Hauptmenü sowie die dazugehörigen Pages im Ordner `/pages` hinzu. Schließlich staged, committet und pusht er das Ganze als einen initialen Commit.

```
## tägliche Routine: aktuell aktiven Branch herausfinden
## und aktuellen Stand herunterladen
git status
git pull

## Ordner und Dateien anlegen
mkdir "website"
cd website
echo "Hauptmenu: Projekt, Korpus, Schema" > index.html
mkdir "pages"
cd pages
echo "Lorem Ipsum" > projekt.html
echo "Lorem Ipsum" > korpus.html
echo "Lorem Ipsum" > schema.html
cd ../..

## alle Änderungen stagen
git add --all

## Commit erstellen
git commit -m "Initialer Commit für die Website"

## Commit hochladen
git push
```

----------

<img src="../../images/attention_symbol.png" alt="Attention" width="25"/> *Im Terminal in das Repo von UserB wechseln.*

**UserB** führt seine tägliche Arbeitsroutine durch. Mit der Entwicklung einer Suchfunktion betraut, erzeugt er anschließend einen dafür gedachten lokalen Branch `suchfunktion`:

```
## prüfen, auf welchem Branch man sich lokal befindet
git status

## die neuesten Daten vom Remote-Branch herunterladen
## und direkt in den lokalen Branch einfügen
git pull

## neuen lokalen Branch anlegen: der Basis-Commit dieses
## neuen Branches ist der letzte Commit des master-Branches,
## da wir uns gerade auf master befinden
git checkout -b suchfunktion

## prüfen, auf welchem Branch wir uns jetzt befinden
git status

## Liste aller Branches (lokal und remote) mit
## letztem Commit und ggf. Verknüpfung zwischen
## lokalem und Remote-Branch anzeigen lassen
git branch -avv
```

Die Übersicht über die Branches verrät, dass es zu `suchfunktion` aktuell noch keinen dazugehörigen Remote-Branch `remotes/origin/suchfunktion` gibt. Im Moment hat UserB nur einen lokalen Branch angelegt: Dieser wird erst mit `git push` vom Branch `suchfunktion` aus veröffentlicht. Prinzipiell kann man Branches auch als reine Lokal-Branches nutzen, die man nicht veröffentlichen wollen: Pushes von anderen lokalen Branches aus veröffentlichen `suchfunktion` nicht.

UserB entwickelt im Folgenden das Feature, staged und committet die Änderungen und macht seinen Feature-Branch im Remote-Repo für die anderen Projekt-Mitglieder sichtbar:

```
## in index.html den neuen Menüpunkt "Suche" hinzufügen
cd website
rm -f index.html
echo "Hauptmenu: Projekt, Korpus, Schema, Suche" > index.html

## neue Suche-Page hinzufügen
cd pages
echo "Lorem Ipsum" > suche.html
cd ../..

## alle Änderungen stagen
git add --all

## Commit erzeugen
git commit -m "Suchfunktion implementiert"

## Commit und damit auch den Feature-Branch
## in das Remote-Repo hochladen
git push --set-upstream origin suchfunktion

## Mit der Flag "--set-upstream origin suchfunktion"
## wird eine Verknüpfung zwischen dem lokalen und dem
## Remote-Branch "suchfunktion" gesetzt. Damit
## werden in Zukunft "git push" und "git pull" wissen,
## woher sie die Daten beziehen und wohin sie sie
## hochladen müssen, wenn das lokale Repo sich auf
## dem "suchfunktion"-Branch befindet.

## Liste aller Branches anzeigen lassen
git branch -avv
```

Nun ist der Branch `suchfunktion` mit dem Such-Feature veröffentlicht und kann von den Team-Mitgliedern besichtigt werden.

----------

<img src="../../images/attention_symbol.png" alt="Attention" width="25"/> *Im Terminal in das Repo von UserA wechseln.*

**UserA** lädt sich den neuen Branch herunter...

```
## den neusten Stand des Remote-Repos herunterladen
git pull

## den neuen Branch "suchfunktion" aktivieren:
## dies erzeugt einen lokalen Branch "suchfunktion"
## und verknüpft ihn automatisch mit dem
## Remote-Branch
git checkout suchfunktion
```

... und testet das neue Feature.

----------

<img src="../../images/attention_symbol.png" alt="Attention" width="25"/> *Im Terminal in das Repo von UserB wechseln.*

**UserB** bekommt grünes Licht. Da alle zufrieden sind, wird entschieden, das Such-Feature so, wie es jetzt ist, in den `master`-Branch aufzunehmen, also zum festen Bestandteil der Website zu machen. Dafür wird der Branch `suchfunktion` nun in den `master` gemergt, was bedeutet, dass in `master` ein neuer Commit erzeugt wird, der den Stand des letzten Commits auf dem `suchfunktion`-Branch mit dem Stand des letzten `master`-Commits vereint. Der Branch `suchfunktion` bleibt dabei, wie er ist.

Dieser Vorgang wird in unserem Fall ohne KOnflikte ablaufen, da seit der Erstellung des `suchfunktion`-Branches auf dem `master`-Branch kein neuer Commit hinzugekommen ist: Es ist, als hätte UserB die Suchfunktion direkt auf dem `master` entwickelt. Diese konfliktfreie Art des Mergens nennt man auch *Fast-forward*.

```
## auf den Branch wechseln, in den die Änderungen
## des anderen Branches integriert werden sollen
git checkout master

## sicherstellen, dass man die neuesten Commits
## vom Remote-Master lokal zur Verfügung hat
git pull

## den lokalen suchfunktion-Branch in den
## lokalen master-Branch mergen
git merge suchfunktion
```

Der Merge war erfolgreich, er hat aber lokal stattgefunden. Mit `git push` wird der neue Zustand des `master`-Branches nun in das Remote-Repo geladen.

```
## den neuen Zustand vom master pushen
git push

## aktualisierte Liste der lokalen und
## Remote-Branches anschauen
git branch -avv
```

Da das Feature implementiert und von allen Team-Mitgliedern abgenickt ist, hat der Branch `suchfunktion` seinen Zweck erfüllt. Um die History und Liste der Branches übersichtlich zu halten, ist es üblich, an dieser Stelle den Feature-Branch zu löschen. Für zukünftige größere Änderungen an der Website, wie eben die Entwicklung eines neuen Features oder einer neuen Page, werden immer jeweils neue Feature-Branches ins Leben gerufen.

```
## den lokalen Branch "suchfunktion" löschen
git branch -d suchfunktion

## Hinweis: Dieser Befehl wird scheitern, wenn
## die Remote-Version und die lokale Version des
## Branches sich unterscheiden. Um auch in diesem
## Fall den lokalen Branch zu löschen, muss die
## Flag "-D" verwendet werden: "git branch -D suchfunktion".

## den Remote-Branch "suchfunktion" löschen
git push -d origin suchfunktion

## aktualisierte Liste der lokalen und
## Remote-Branches anschauen
git branch -avv
```

----------

<img src="../../images/attention_symbol.png" alt="Attention" width="25"/> *Im Terminal in das Repo von UserA wechseln.*

**UserA** will sich einige Zeit später nochmal den `suchfunktion`-Branch anschauen und versucht den neuesten Stand zu pullen, bekommt aber natürlich eine Fehlermeldung, da UserB den Branch im Remote-Repo ja schon gelöscht hat. UserA schaut verwirrt auf die Branch-Liste, die `git branch -avv` ihm ausgibt: Da wird `remotes/origin/suchfunktion` noch als existent angezeigt.

Das Problem ist hier, dass ein einfacher `git fetch`-Befehl (der mit `git pull` ausgeführt wird) nicht ausreicht, um die Liste der lokalen Verknüpfungen zu den Remote-Branches zu aktualisieren: Alle anderen User müssen, nachdem ein User einen Remote-Branch löscht, ihre lokalen Verknüpfungen aktualisieren: `git fetch --all --prune`. Damit werden alle lokale Verknüpfungen zu Remote-Branches, die nicht mehr existieren, entfernt und `git branch -avv` sollte nun die aktuelle Situation des Remote-Repos korrekt anzeigen.

Was UserA bleibt, ist sein lokaler `suchfunktion`-Branch. Da er weiß, dass UserB inzwischen die Suchfunktion in den Master gemergt hat, aktiviert er seinen lokalen Master, aktualisiert ihn und löscht den nun funktionslosen lokalen `suchfunktion`-Branch.

```
## die korrekte Branch-Liste vom Remote-Repo beziehen:
## von allen Branches den neuesten Stand beziehen
## und lokale Verknüpfungen zu nicht mehr
## existierenden Remote-Branches löschen
git fetch --all --prune

## auf den Master-Branch wechseln, um im
## Anschluss die neuesten Commits zu beziehen
## und den lokalen suchfunktion-Branch
## löschen zu können
git checkout master

## den Merge-Commit auf dem Master-Branch
## von UserB beziehen (mit "git pull"
## fetchen wir nochmal den neusten Stand, was wir
## zwei Befehle zuvor schon einmal getan haben,
## und mergen ihn in den lokalen Master.
## Alternativ könnte man hier also anstatt "pull"
## auch nur "merge" verwenden: "git merge origin master".)
git pull

## den lokalen suchfunktion-Branch löschen
git branch -d suchfunktion
```

Davon abgesehen ändert UserA noch eine Kleinigkeit in der `index.html` und im `/pages`-Ordner: Den Titel der Seite "Schema" schreibt er in "Schemata" um, da das Projekt eventuell mehrere Schemata publizieren wird. Am Ende staged, committet und pusht er die Änderungen.

```
## prüfen, ob er auf dem master-Branch ist
git status

## falls nötig: auf den lokalen master-Branch wechseln
git checkout master

## index.html ändern
cd website
rm -f index.html
echo "Hauptmenu: Projekt, Korpus, Schemata, Suche" > index.html

## Name von schema.html ändern
cd pages
rm -f schema.html
echo "Lorem Ipsum" > schemata.html
cd ../..

## stagen
git add --all

## committen
git commit -m "Menupunkt 'Schema' in 'Schemata' umbenannt"

## pushen
git push
```

----------

<img src="../../images/attention_symbol.png" alt="Attention" width="25"/> *Im Terminal in das Repo von UserB wechseln.*

**UserB** entwickelt derweil eine Changelog-Seite auf einem neuen Feature-Branch `changelog`. In unserer fiktiven Timeline hat UserB damit bereits begonnen, bevor UserA seine letzten Änderungen committet hat. Das heißt in dieser Situation: UserB hat keine Chance, die letzten Änderungen von UserA zu pullen, bevor er den neuen Branch `changelog` eröffnet.

```
## prüfen, auf welchem Branch wir uns befinden
git status

## den aktuellsten Commit auf dem master-Branch anschauen
git show HEAD

## UserB kennt also zu diesem Zeitpunkt den Commit
## von UserA nicht, mit dem er den Menupunkt
## "Schema" in "Schemata" geändert hat.

## neuen lokalen Branch anlegen: der Basis-Commit
## dieses neuen Branches ist der zu diesem Zeitpunkt
## für UserB bekannte letzte Commit des
## master-Branches, der jenige mit dem
## Kommentar "Suchfunktion implementiert"
git checkout -b changelog

## prüfen, auf welchem Branch wir uns jetzt befinden
git status

## Liste aller Branches anzeigen lassen
git branch -avv

## in index.html den neuen Menüpunkt "Changelog" hinzufügen
cd website
rm -f index.html
echo "Hauptmenu: Projekt, Korpus, Schema, Suche, Changelog" > index.html

## neue Suche-Page hinzufügen
cd pages
echo "Lorem Ipsum" > changelog.html
cd ../..

## alle Änderungen stagen
git add --all

## Commit erzeugen
git commit -m "Changelog implementiert"

## Commit und damit auch den Feature-Branch
## in das Remote-Repo hochladen
git push --set-upstream origin changelog

## Liste aller Branches anzeigen lassen
git branch -avv
```

Damit ist der `changelog`-Branch im Remote-Repo veröffentlicht. Auch dieses Feature wird vom Projekt-Team positiv aufgenommen. UserB bekommt erneut grünes Licht zum Mergen des Feature-Branches in den `master`. 

```
## auf den Branch wechseln, in den die
## Änderungen des anderen Branches
## integriert werden sollen
git checkout master

## sicherstellen, dass man die neuesten
## Commits vom Remote-Master lokal
## zur Verfügung hat
git pull

## Hier merkt UserB nun, dass der master-Branch
## des remote-Repos seit dem lokalen Erzeugen
## des changelog-Branches einen neuen Commit
## erhalten hat. Am ursprünglichen Plan,
## die Branches zu mergen, ändert das jedoch
## nichts: Git bietet die Features an,
## mit möglichen Konflikten zwischen
## den Commits umzugehen.

## den lokalen changelog-Branch in
## den lokalen master-Branch mergen
git merge changelog
```

UserB bekommt eine Konflikt-Meldung, da UserA und UserB die gleiche Zeile in der gleichen Datei `index.html` verändert haben:

```
Auto-merging website/index.html
CONFLICT (content): Merge conflict in website/index.html
Automatic merge failed; fix conflicts and then commit the result.
```

Git übergibt die Kontrolle folgerichtig dem User. Um den Konflikt manuell zu lösen, muss UserB die `index.html` in seinem Working Tree mit einem Text- oder Code-Editor öffnen. Dort fällt erneut auf, dass Git den Inhalt der Datei verändert hat:

```
<<<<<<< HEAD
Hauptmenu: Projekt, Korpus, Schemata, Suche
=======
Hauptmenu: Projekt, Korpus, Schema, Suche, Changelog
>>>>>>> changelog
```

Wie gehabt: Die Schreibweise markiert hier, wo der Konflikt sich genau befindet und welche Version der betroffenen Zeile im Dokument von welchem Repo kommt. `<<<<<<<` markiert den Beginn des Konflikts. Die folgenden Zeilen bis `=======` sind die Version des sogenannten Basis- oder Head-Branches, also des Branches, der das Ziel der Merge-Prozedur ist - in diesem Fall ist das die Version von UserA auf dem lokalen `master`-Branch von UserB. Zwischen `=======` und `>>>>>>>` folgt die Version des Branches, der in den Basis-Branch gemergt werden soll: die Version von UserB aus `changelog`.

Um den Konflikt zu lösen, müssen die drei Zeilen mit den von Git gesetzten Steuerzeichen komplett gelöscht werden. UserB kann sich für eine der Varianten entscheiden oder die Zeile anders schreiben. Die Lösung hier ist `Hauptmenu: Projekt, Korpus, Schemata, Suche, Changelog`. Am Ende muss die Datei gespeichert, neu gestaged und committet werden. Wenn das für alle Dateien mit manuell zu lösenden Merge-Konflikten erledigt ist, ist der `merge`-Vorgang abgeschlossen. Der aktualisierte lokale `master` mit dem neuen Merge-Commit kann dann sofort gepusht werden.

```
## stagen nach dem manuellen Lösen des Konflikts
git add website/index.html

## commit
git commit -m "Merge-Konflikt gelöst: Changelog in Master integriert"

## Abweichungen zwischen den Branches anschauen
git log --graph

## pushen
git push

## aktuelle Liste der lokalen und Remote-Branches anschauen
git branch -avv
```

Damit ist der Merge abgeschlossen. Schließlich löscht UserB den Feature-Branch lokal und im Remote-Repository:

```
## den lokalen Branch "changelog" löschen
git branch -d changelog

## Erneut der Hinweis: Dieser Befehl wird scheitern,
## wenn die Remote-Version und die lokale Version
## des Branches sich unterscheiden. Um auch in diesem
## Fall den lokalen Branch zu löschen, muss die
## Flag "-D" verwendet werden: "git branch -D changelog".

## den Remote-Branch "changelog" löschen
git push -d origin changelog

## aktuelle Liste der lokalen und Remote-Branches anschauen
git branch -avv
```

Das Projekt-Team ist sich einig, nun den ersten Release der Website zu veröffentlichen. Dafür wird UserB auf der Basis des letzten Commits auf dem Master-Branch einen neuen Branch `releases` erzeugen:

```
## prüfen, auf welchem Branch man sich befindet
git status

## releases-Branch anlegen
git checkout -b release-0.1.0

## den releases-Branch pushen
git push --set-upstream origin releases
```

Nun kann das Team die Website testen und Bug-Fixes auf dem `releases`-Branch einbauen. In der gleichen Zeit kann die Arbeit auf dem `master`-Branch weitergehen und es können beispielsweise bereits Features integriert werden, die erst für kommende Releases vorgesehen sind.

Wenn der Stand der Website auf dem `releases`-Branch ausreichend getestet und für release-würdig befunden wurde, wird einem Commit des Branches ein Release-Tag zugewiesen:

```
## die hash-ID des aktuellen Commits heraussuchen
git show HEAD
git log --oneline

## Release-Tag "v0.1.0" auf dem aktuellen Commit erzeugen
git tag -a v0.1.0 <hash-ID des aktuellen Commits> -m "Release v0.1.0"

## den Tag pushen
git push --tags
```

Mit dem Tag ist nun klar definiert, welcher Commit den Release-Zustand der Website darstellt.

An dieser Stelle könnte man nun entweder auf dem `master` weiterarbeiten und kurz vor dem nächsten Release den dann aktuellen `master`-Stand erneut in den `release`-Branch mergen, um entsprechend die nächsten Release vorzubereiten. Es ist aber auch sinnvoll, Bug-Fixes und Änderungen, die zur Herstellung eines Releases auf dem `release`-Branch vorgenommen worden sind, unverzüglich danach wieder in den `master` einzuspeisen. Ansonsten bekommt der `master`-Branch diese Änderungen nie zu sehen.

Es gibt praktisch viele verschiedene Git-Workflows, die auch das Erzeugen von Releases verschieden regeln. Einen Tag zum entsprechenden Commit auf dem Arbeitsbranch zuzuweisen, ist bereits eine ausreichende Maßnahme, um einen Commit klar als Release zu markieren. Einen oder mehrere Release-Branches zu verwenden oder darüberhinaus auch noch Branches für einzelne Bugfixes ist dagegen eine Strategie, die sich für größere Entwicklerteams eignet, in denen die Arbeit untereinander verteilt und also auch parallel an verschiedenen Problemen gearbeitet werden kann. Eine kleine Übersicht über verschiedene Git-Workflows findet ihr [hier](https://web.archive.org/web/20230609105102/https://about.gitlab.com/topics/version-control/what-is-git-workflow/).

Das ist das Ende des zweiten Szenarios. Es ist zu sehen gewesen, wie die täglichen Abläufe im kollaborativen Arbeiten mit Git aussehen, welche Befehle dabei immer wieder zum Einsatz kommen und vor allem: dass ein Konflikt zwischen zwei Versionen einer Datei (in Git-Begriffen: zwischen zwei zu mergenden Commits) kein unbedingt zu verhindernder Ausnahmezustand ist, sondern zum kollaborativen Arbeiten mit Dateien (auch ohne Git) dazugehört. Git hält gezielt Features dafür bereit, mit dieser Situation umzugehen.

Diese und die anderen Features müssen nicht zwingend über die Kommandozeile bedient werden. Im Folgenden stelle ich euch drei Tools mit grafischer Benutzeroberfläche vor. Ihr habt bis hier schon viele Befehle kennengelernt (falls noch nicht geschehen: ein Blick in den [Was ist ... ](../was-ist.md)-Abschnitt ist dafür empfohlen). Zu wissen, wie Git auf Ebene der Kommandozeile funktioniert, hilft enorm, die Funktionen dieser Tools zu verstehen.

## Tools für die Arbeit mit lokalen Repositorien
Die am häufigsten gebrauchten Git-Befehle zu kennen ist nützlich. Im täglichen Einsatz beschleunigen Git-Tools mit grafischen Benutzeroberflächen die Arbeit jedoch enorm. Drei von ihnen werden im Folgenden vorgestellt.
### Git Client Add-On für Oxygen XML Editor
[Download](https://www.oxygenxml.com/doc/versions/25.1/ug-editor/topics/git-addon.html)

- Betriebssysteme: Win / Linux / Mac
- Plugin für Oxygen XML Editor 24.1 oder neuer
- für lokale Repos und Repos von allen gängigen Online-Plattformen (GitHub, GitLab) verwendbar
- besonders geeignet für Projekte mit XML-Dateien

#### Installation
Öffnet nebeneinander Oxygen und einen Browser mit der Download-Page des Plugins. Nun könnt ihr den *Install*-Button von der Download-Page per Drag and Drop in Oxygen hineinziehen: Der Installationsprozess startet dadurch automatisch. Falls das nicht funktionieren sollte, gibt es noch eine Anleitung zur manuellen Installation auf der Download-Page.

#### Neue Fenster
Mit dem Plugin könnt ihr drei neue Fenster in Oxygen öffnen: **Git Branch Manager**, **Git Historie** und **Git Staging**.

<img src="../../images/screenshot_git-client-add-on-fuer-oxygen-xml-editor_01.png" alt="Fenster anzeigen: Git Branch Manager, Git Historie, Git Staging" title="Fenster anzeigen: Git Branch Manager, Git Historie, Git Staging" width="700" style="border: 2px solid black"/>

##### Git Staging
*Git Staging* werdet ihr am häufigsten verwenden. Im Dropdown-Menü *Arbeitskopie* wählt ihr das Repository aus, das ihr verwalten wollt. Im Beispiel ist `an-online-repo` eingestellt.

Um ein Repository als Eintrag in das Dropdown-Menü einzufügen, gibt es zwei Wege: Klicke auf das Symbol mit dem drei Punkten in der oberen rechten Ecke und klone ein neues Online-Repository. Oder klicke auf das Ordner-Symbol unter dem Symbol mit den drei Punkten und wähle damit einen Ordner mit einem schon existierenden lokalen Repo auf deinem System aus.

Nicht gestagte lokale Änderungen deines moment ausgewählten Repos werden automatisch im Feld *Nicht-aufgeführte (unstaged) Dateien* aufgelistet und können per Klick auf die Pfeilsymbole rechts über dem Feld nach unten in das Feld *Aufgeführte (staged) Dateien* verschoben werden.

Wenn sich im Feld *Aufgeführte (staged) Dateien* Einträge befinden, kann ein lokaler Commit angefertigt werden: Schreibt dafür eine Commit-Nachricht in das Feld *Commit-Nachricht* und klickt unten rechts auf den Button *Commit*.

Um Commits zu in das Online-Repo zu pushen, klickt auf das Symbol mit dem nach oben zeigenden Pfeil ganz oben links im Fenster. Um neue Commits aus dem Online-Repo herunterzuladen, klicke auf den nach unten zeigenden Pfeil. Damit werden die Änderungen nicht nur gefetcht, sondern auch gleich in dein lokales Repo eingefügt - standardmäßig und empfohlenerweise durch `merge`.

<img src="../../images/screenshot_git-client-add-on-fuer-oxygen-xml-editor_03.png" alt="Fenster: Git Staging" title="Fenster: Git Staging" width="700" style="border: 2px solid black"/>

##### Git Branch Manager
*Git Branch Manager* zeigt euch die derzeit lokal bekannten lokalen und Remote-Branches an. Hier könnt ihr den aktuellen lokalen Branch wechseln (*auschecken*) und neue lokale Branches anlegen sowie löschen. Klickt mit der rechten Maustaste auf einen Branch, um die zur Verfügung stehenden Optionen zu sehen.

<img src="../../images/screenshot_git-client-add-on-fuer-oxygen-xml-editor_04.png" alt="Fenster anzeigen: Git Branch Manager, Git Historie, Git Staging" title="Fenster anzeigen: Git Branch Manager, Git Historie, Git Staging" width="700" style="border: 2px solid black"/>

##### Git Historie
*Git Historie* zeigt euch die gesamte Historie des Repos an und bietet euch Möglichkeiten zum Filtern der Ansicht sowie spezielle Aktionen, die die einzelnen Commits betreffen. Hier können etwa neue Branches von Commits aus erzeugt werden. Klickt mit der rechten Maustaste auf einen Commit, um die zur Verfügung stehenden Aktionen zu sehen. Es handelt sich um:

- Branch erstellen (`git checkout -b <name of new branch>`: vom ausgewählten Commit aus einen neuen lokalen Branch beginnen),
- Tag erstellen (`git tag -a <tag name> <hash value of commit> -m "Tag-Kommentar"`: für den ausgewählten einen neuen lokalen Tag definieren),
- Checkout (`git checkout <hash value of commit>`: den ausgewählten lokalen Commit auschecken und damit in den detached HEAD-Modus wechseln),
- Commit zurücknehmen (`git revert <hash value of commit`: einen neuen Commit im aktuell ausgewählten lokalen Branch erzeugen, der die Änderungen des ausgewählten Commits rückgängig macht),
- "Branch" auf diesen Commit zurücksetzen (`git reset <hash value of commit>` mit den Flags `--soft`, `--mixed` oder `--hard`: den Branch auf den Stand des ausgewählten Commits zurücksetzen, mehr dazu in [Experiment #7](../7-fehler-rueckgaengig-machen/))

<img src="../../images/screenshot_git-client-add-on-fuer-oxygen-xml-editor_05.png" alt="Fenster anzeigen: Git Branch Manager, Git Historie, Git Staging" title="Fenster anzeigen: Git Branch Manager, Git Historie, Git Staging" width="700" style="border: 2px solid black"/>

#### Merge-Konflikte in der Benutzeroberfläche lösen

<img src="../../images/under-construction_symbol.png" alt="Under Construction" width="30" /> to be added

### Visual Studio Code (VSCode) mit Git Graph-Extension
[Download](https://code.visualstudio.com/Download)

- Betriebssysteme: Win / Linux / Mac
- Code-Editor mit Git-Funktionen
- für lokale Repos und Repos von allen gängigen Online-Plattformen (GitHub, GitLab) verwendbar

<img src="../../images/under-construction_symbol.png" alt="Under Construction" width="30" /> to be added

<!-- hier weiter -->
### GitHub Desktop
[Download](https://desktop.github.com/)

- Betriebssysteme: Win / Mac
- Software zum Verwalten von Git-Repositorien
- für lokale Repos und Repos von allen gängigen Online-Plattformen (GitHub, GitLab) verwendbar

<img src="../../images/under-construction_symbol.png" alt="Under Construction" width="30" /> to be added

<!-- hier weiter -->

[Zurück zum Anfang](#1-ein-einfacher-kollaborativer-git-workflow)

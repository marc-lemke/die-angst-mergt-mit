# 3. Aliase
Git bietet die Möglichkeit, für git-Befehle Aliase zu definieren. Benutzt man häufiger einen langen Befehl wie `git log --oneline --graph --decorate --all`, der im Terminal eine kurze Repräsentation der Repo-Historie anzeigt, kann man global einen Alias dafür anlegen: `git config --global alias.lol "log --oneline --graph --decorate --all"`. Von nun an könnt ihr tippen: `git lol`.

# Versionskontrolle

Dieses Jupyter Notebook enthält die Beispiele zur Vorlesung Versionsverwaltung mit Git. Um dieses Notebook auszuführen benötigt man einen [Bash-Kernel](https://github.com/takluyver/bash_kernel). Alle Kommandos in diesem Notebook simulieren die Ausführung an der Kommandozeile.

## Diff

Bevor wir uns Git zuwenden, müssen wir zunächst die Darstellung von Unterschieden zwischen Dateien betrachten. Man nennt so einen Unterschied einen *Diff*, und erstellt werden diese vom klassischen Werkzeug `diff`.

Gegeben seien zwei Beispieldateien (deren Inhalt wir uns mit `cat` ansehen können):


```bash
cat data/git/Example1.java
```

    public class Example {
      public boolean foo(int x, int y) {
        if(x < y)
          return true;
    
        return false;
      }
    }



```bash
cat data/git/Example2.java
```

    public class Example {
      public boolean foo(int v, int w) {
        if(v < w)
          return true;
    
        return false;
      }
    }


Der Unterschied zwischen den beiden Dateien kann mit `diff <datei1> <datei2>` angezeigt werden.


```bash
diff data/git/Example1.java data/git/Example2.java
```

    2,3c2,3
    <   public boolean foo(int x, int y) {
    <     if(x < y)
    ---
    >   public boolean foo(int v, int w) {
    >     if(v < w)




Angezeigt werden nur jene Zeilen, bei denen sich die beiden Dateien unterscheiden. Zeilen mit dem Prefix `<` enstammen der ersten Datei, und jene mit dem Prefix `>` sind die entsprechenden Zeilen in der zweiten Datei.

Sehen wir uns ein Beispiel an, bei dem neue Zeilen im Vergleich zur ersten Datei hinzugefügt wurden:


```bash
cat data/git/Example3.java
```


```bash
diff data/git/Example1.java data/git/Example3.java
```

Nun noch ein Beispiel bei dem Zeilen entfernt wurden.


```bash
cat data/git/Example4.java
```


```bash
diff data/git/Example1.java data/git/Example4.java
```

### Context

Um einen Diff besser zu verstehen, kann man sich den Kontext, also die Zeilen rund um die Änderung, mitanzeigen lassen.


```bash
diff -c data/git/Example1.java data/git/Example2.java
```


```bash
diff -c data/git/Example1.java data/git/Example3.java
```


```bash
diff -c data/git/Example1.java data/git/Example4.java
```

### Unified diff format

Als Austauschformat wird das _Unified_-Diff-Format verwendet


```bash
diff -u data/git/Example1.java data/git/Example2.java
```

Zeilen mit Präfix `+` wurden hinzugefügt, mit Präfix `-` entfernt.


```bash
diff -u data/git/Example1.java data/git/Example3.java
```


```bash
diff -u data/git/Example1.java data/git/Example4.java
```

## Patch

Das Unified Diff Format erlaubt es, bestehende Dateien anzupassen. Der Unterschied wird dazu in einem *Patch* gespeichert.


```bash
diff -u data/git/Example1.java data/git/Example2.java > patch.txt
```


```bash
cat patch.txt
```


```bash
cp data/git/Example1.java Example.java
```


```bash
cat Example.java
```

Um die Datei `Example.java` nun basierend auf unserem Patch zu verändern, benötigen wir das Kommando `patch`.


```bash
patch -p0 Example.java < patch.txt
```


```bash
cat Example.java
```


```bash
# Nur um das Jupyter Notebook aufzuräumen...
rm patch.txt
rm Example.java
```

## Git

Wir verwenden zur Versionsverwaltung das Programm `git`. Git ist in alle modernen IDEs direkt integriert, wir verwenden hier jedoch die Kommandozeilen-Version.

Zunächst legen wir uns nur ein temporäres Verzeichnis an, damit das Jupyter Notebook nicht zugemüllt wird...


```bash
rm -rf tmp
mkdir -p tmp
cd tmp
```

Wir nehmen an Entwickler 1 hat einen Workspace im Verzeichnis `dev1.


```bash
mkdir -p dev1
cd dev1
```

Wir legen hier nun ein neues Git Repository an.


```bash
git init
```

Um Veränderungen im Workspace zu simulieren, fügen wir eine Datei `hello.txt` hinzu.


```bash
echo "Hallo SE 2023" > hello.txt
```


```bash
ls
```

Um diese neue Datei in die Versionsverwaltung aufzunehmen, dient das `add` Kommando in Git.


```bash
git add hello.txt
```

Damit befindet sich die Datei auf der "Stage". Um sie ins Repository einzuchecken, dient das `commit` Kommando.


```bash
git commit -m "Add hello"
```

Git-Output wird mit einem "Pager" angezeigt, der per Default auf einen Tastendruck wartet. Nachdem das im Jupyter Notebook nicht geht, müssen wir den an dieser Stelle umkonfigurieren. Das ist normalerweise nicht notwendig.


```bash
# Only required for presenting in the Jupyter notebook
export GIT_PAGER=cat
```

Die Historie unserers Repositories können wir mit `log` ansehen.


```bash
git log
```

Nun verändern wir die Datei nochmal (und simulieren damit, dass Entwickler 1 arbeitet).


```bash
echo "Hello again" >> hello.txt
```

Den Status unseres Workspaces können wir mit `status` einsehen.


```bash
git status
```

Wenn wir wissen wollen, was verändert wurde, können wir uns einen Diff anzeigen lassen.


```bash
git diff
```

Das Committen der neuen Änderungen funktioniert wieder ähnlich.


```bash
git add hello.txt
```


```bash
git commit -m "Updated hello"
```

Die Historie zeigt uns nun zwei Änderungen.


```bash
git log
```

Wenn wir wissen wollen, wer zuletzt die Datei `hello.txt` geändert hat, dann finden wir das mit `blame` heraus.


```bash
git blame hello.txt
```

### Nicht-committete Änderungen rückgängig machen

Angenommen wir nehmen wieder eine lokale Änderung vor.


```bash
echo "Hello yet again" >> hello.txt
```


```bash
git status
```

Wenn wir lokale Änderungen, die noch nicht "staged" oder "committed" sind, rückgängig machen wollen, dann können wir dazu einfach die Version in `HEAD` auschecken.


```bash
git checkout hello.txt
```


```bash
git status
```


```bash
cat hello.txt
```

### Unstaging

Nehmen wir noch eine Änderung vor:


```bash
echo "Hello yet again" >> hello.txt
git add hello.txt
```


```bash
git status
```

Diese Änderung ist nun schon "staged". Wenn wir diese Änderung rückgängig machen wollen, reicht ein "checkout" nicht aus:


```bash
git checkout hello.txt
```


```bash
git status
```

Zuvor müssen wir die Datei "unstagen":


```bash
git reset hello.txt
```


```bash
git status
```

Aber committen wir die Änderung zunächst doch noch.


```bash
git add hello.txt
git commit -m "Hello the third"
```

### Reverting

Wir können eine beliebige Version der Datei `hello.txt` auschecken. Die folgenden Commits existieren:


```bash
git log
```

Da sich Commit-Hashes bei jeder Ausführung ändern, holen wir uns den Commithash des ersten Commits (das ist normal nicht so notwendig, das passiert hier nur um mit dem Jupyter Notebook zu arbeiten).


```bash
# Normally you'd know the commit hash, we need to figure out this way for the Jupyter notebook
COMMIT=$(git log --reverse --oneline | head -n 1 | cut -d ' ' -f1)
```

Wir können uns nun gezielt die Version von `hello.txt` mit diesem Commithash auschecken.


```bash
git checkout $COMMIT -- hello.txt
```


```bash
git status
```


```bash
cat hello.txt
```

Die Datei zählt nun als geändert, wenn wir diese Änderung behalten wollen (und damit die letzten beiden Commits rückgängig machen), dann müssen wir sie commiten.


```bash
git add hello.txt
git commit -m "Reverted to original version"
```


```bash
git log hello.txt
```

### Detached Head

Vorsichtig muss man sein, wenn man nicht nur eine Datei in einer alten Version auscheckt, sondern den ganzen Workspace in den Zustand versetzt.


```bash
git checkout $COMMIT
```

Wenn wir uns die Historie ansehen, sind die letzten beiden Commits nun verschwunden:


```bash
git log --oneline
```

Noch ist nichts verloren, wir können einfach wieder die "main" Version auschecken.


```bash
git checkout main
```


```bash
git log --oneline
```

`main` ist ein Branch. Um zu sehen, wie wir dieses Problem umgehen können, schauen wir uns zunächst wie man mit Branches arbeitet.

## Branches

Zwischen Branches wechselt man mit dem Kommando `checkout`; einen neuen Branch erstellt man mit `checkout -b`.


```bash
git checkout -b se2
```

Nun können wir in unserem `se2` Branch arbeiten.


```bash
echo "Hello in the se2 branch" >> hello.txt
```


```bash
git add hello.txt
```


```bash
git commit -m "Add branch hello"
```


```bash
git log --oneline
```

Wenn wir zurück in den `main` Branch wechseln, dann ist die letzte Änderung hier nicht vorhanden.


```bash
git checkout main
```


```bash
cat hello.txt
```


```bash
git log --oneline
```

Um Branches zusammenzuführen, dient das Kommando "merge". Mergen wir also `se2` nach `main`:


```bash
git merge se2
```

Nun sind die Änderungen wieder zusammengeführt.


```bash
cat hello.txt
```

Änderungen in main sind in `se2` nicht sichtbar:


```bash
echo "Hello added in main" >> hello.txt
git add hello.txt
git commit -m "Added hello main"
```


```bash
git checkout se2
```


```bash
cat hello.txt
```

Wir führen nun auch in `se2` eine Änderung durch und committen.


```bash
echo "Hello added again in se2" >> hello.txt
git add hello.txt
git commit -m "Added hello to se2 again"
```

Diese Änderung ist natürlich in `main` nicht sichtbar.


```bash
git checkout main
```


```bash
cat hello.txt
```

Wenn wir nun allerdings einen Merge probieren, gibt es ein Problem: Die Datei `hello.txt` wurde in beiden Branches editiert.


```bash
git merge se2
```

Der Merge-Conflict wird in der Datei angezeigt.


```bash
cat hello.txt
```


```bash
git status
```

Hier hat der automatische Merge nicht geklappt. Wir müssen also per Hand in unserem Editor das Problem lösen.


```bash
cat > hello.txt << EOM
Hallo SE 2022
Hello in the se2 branch
Hello added in main
Hello added again in se2
EOM
```


```bash
cat hello.txt
```

Wir befinden uns noch im Merge, und müssen diesen abschliessen indem wir die Datei, in der wir den Merge-Conflict behoben haben, stagen und dann committen.


```bash
git status
```


```bash
git add hello.txt
```

Ein Merge-Commit braucht keine explizite Commit-Message.


```bash
git commit --no-edit
```


```bash
git log --oneline
```

Wir können uns hierzu auch einen Commit-Graphen anzeigen lassen.


```bash
git log --oneline --graph
```

### Detached Head reparieren

Erstellen wir nochmal den problematischen Detached Head:


```bash
# Checkout first commit
git checkout $COMMIT
```

Die Lösung des Problems liegt darin, für den aktuellen Zustand einen neuen Branch anzulegen.


```bash
git checkout -b new_feature
```


```bash
echo "Adding hello to initial hello" >> hello.txt
```


```bash
git add hello.txt
```


```bash
git commit -m "Added hello in branch"
```


```bash
git log --oneline --graph
```

Diese Änderung ist natürlich noch nicht im Main-Branch:


```bash
git checkout main
```


```bash
cat hello.txt
```

## Rebase

Ein Merge erzeugt einen expliziten Merge-Commit und eine Verzweigung in der Commit-Historie. Eine Alternative dazu ist ein Rebase, welches die Commits eines Branches auf einen alternativen Startpunkt anwendet. Wir erzeugen uns dazu wieder einen Branch der von dem ersten Commit verzweigt.


```bash
git checkout $COMMIT
```


```bash
git checkout -b rebase_feature
```

In diesem Branch erzeugen wir eine neue Datei `hello2.txt`.


```bash
echo "Hello again" > hello2.txt
```


```bash
git add hello2.txt
git commit -m "Add new hello file"
```

In diesem Branch fehlen uns nun aber die ganzen Änderungen, die an der Datei `hello.txt` angwendet wurden:


```bash
cat hello.txt
```


```bash
git log --oneline --graph
```

Wir können den Branch `rebase_feature` aber mit dem aktuellen Main rebasen:


```bash
git rebase main
```

Hierdurch wird der Commit, mit dem `hello2.txt` erstellt wurde, auf den Main angewendet.


```bash
cat hello.txt
```


```bash
git log --oneline --graph
```

Die Datei `hello2.txt` existiert aber noch nicht im Main:


```bash
git checkout main
```


```bash
ls
```

Wir müssen den Branch dazu nach Main mergen.


```bash
git merge rebase_feature
```


```bash
ls
```

Nachdem der Branch `rebase_feature` auf Main rebased war, wird ein Fast-Forward-Merge angewendet, d.h. es benötigt keinen eigenen Merge-Commit.


```bash
git log --oneline --graph
```

## Stashing

Es kann vorkommen, dass man Änderungen von anderer Stelle einpflegen muss, bevor das Feature, an dem man gerade arbeitet, fertig ist, sodass man es noch nicht committen will. Man kann die Änderungen, die noch nicht committed sind, auf einen "Stash" schieben.


```bash
echo "Some unfinished change" >> hello.txt
```


```bash
cat hello.txt
```


```bash
git status
```


```bash
git stash
```

Damit sind nun alle Dateien wieder am gleichen Stand wie `master`.


```bash
git status
```


```bash
cat hello.txt
```

Um die lokalen Änderungen wieder einzufügen, verwendet man `stash apply`:


```bash
git stash apply
```


```bash
cat hello.txt
```


```bash
# Houskeeping for Jupyter notebook
cd ..
rm -rf dev1
```

## Mehrere Benutzer und Remote Repositories

Git ist ein verteiles Versionskontrollsystem, d.h. es kann mehrere Klone des Repositories geben, und man kann Änderungen dazwischen austauschen. Wir legen uns für das Notebook ein neues "Remote" Repository an, auf welches 2 simulierte Entwickler Zugriff haben sollen:


```bash
mkdir remote_repo
cd remote_repo
git init --bare
```

Entwickler 1 und Entwickler 2 können sich Klone des Repositories mit dem "clone" Befehl erstellen. (Der Befehl wird aktuell noch meckern dass das Repository leer ist).


```bash
cd ..
```


```bash
git clone remote_repo dev1
```


```bash
git clone remote_repo dev2
```

Zunächst simulieren wir, dass Entwickler 1 in seinem Workspace arbeitet.


```bash
cd dev1
```


```bash
echo "I am developer 1" >> hello.txt
git add hello.txt
git commit -m "Hello 1"
```

Um die Änderungen im eigenen Repository an das "Remote" Repository zu schieben, dient der "push" Befehl:


```bash
git push
```

Entwickler 2 kann nun diese Änderungen vom Remote Repository in das eigene "pullen":


```bash
cd ..
cd dev2
```

Noch ist das Repository leer:


```bash
ls
```

Änderungen ziehen passiert mit "pull":


```bash
git pull
```


```bash
cat hello.txt
```

Nun arbeitet auch Entwickler 2 im eigenen Workspace.


```bash
echo "I am developer 2" >> hello.txt
git add hello.txt
git commit -m "Add hello from developer 2"
```

Die Änderungen können per "push" wieder geteilt werden.


```bash
git push
```

Entwickler 1 kann sich diese Änderungen mit "pull" holen.


```bash
cd ..
cd dev1
```


```bash
cat hello.txt
```


```bash
git pull
```


```bash
git log
```


```bash
cat hello.txt
```


```bash
cd ..
```

## Remotes

Ein Git Repository ist nicht an ein einzelnes Remote-Repository gebunden, sondern kann sich mit beliebig vielen anderen Repositories austauschen. Klonen wir zunächst ein Repository von GitHub:


```bash
git clone https://github.com/se2p/se2022-gitexample.git
```


```bash
cd se2022-gitexample
```

Wenn ein Repository per "clone" erstellt wird, dann heisst das Upstream-Repository `origin`. Wir können uns ansehen, wo `origin` liegt:


```bash
git remote get-url origin
```

Legen wir uns noch einen Klon des neuen Repositories an:


```bash
cd ..
git clone se2022-gitexample another_clone
```


```bash
cd another_clone
```

`origin` ist nun einfach das Verzeichnis unseres lokalen Git-Repositories, das wir eben geklont haben:


```bash
git remote get-url origin
```

Wir aber nun auch das GitHub-Repository als Remote hinzufügen:


```bash
git remote add github https://github.com/se2p/se2022-gitexample.git
```

Unser Repository hat nun zwei Remotes:


```bash
git remote
```

Der Name des Remotes kann bei einem `push` angegeben werden.

### Hinweis zum Pushen von Branches an Remotes

Befindet man sich in einem Branch, der auf einem Remote noch nicht existiert, so resultiert ein `git push` in der folgenden Fehlermeldung:

```
fatal: The current branch Foo has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin <branchname>
```

Durch Eingabe des vorgegebenen Befehls `git push --set-upstream origin <branchname>` wird der Branch am Remote `origin` angelegt. Danach kann man Dateiänderungen direkt in den Remote-Branch pushen. Alternativ erlaubt auch `git push --all` dass _alle_ Branches gepushed werden.

### Hinweis zum Pushen von Tags an Remotes

Wenn nicht nur Dateiänderungen sondern auch Tags gepushed werden sollen, so muss man den Tagnamen der gepushed werden soll angeben: `git push origin <tagname>`. Alternativ pushed `git push --follow-tags` einfach alle Tags.



```bash
# Clean up notebook
cd ../..
rm -rf tmp
```

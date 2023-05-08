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

    public class Example {
      public boolean foo(int x, int y) {
        if(x < y)
          return true;
    
        if(x == y)
          return true;
    
        return false;
      }
    }



```bash
diff data/git/Example1.java data/git/Example3.java
```

    5a6,8
    >     if(x == y)
    >       return true;
    > 




Nun noch ein Beispiel bei dem Zeilen entfernt wurden.


```bash
cat data/git/Example4.java
```

    public class Example {
      public boolean foo(int x, int y) {
    
        return false;
      }
    }



```bash
diff data/git/Example1.java data/git/Example4.java
```

    3,4d2
    <     if(x < y)
    <       return true;




### Context

Um einen Diff besser zu verstehen, kann man sich den Kontext, also die Zeilen rund um die Änderung, mitanzeigen lassen.


```bash
diff -c data/git/Example1.java data/git/Example2.java
```

    *** data/git/Example1.java	Tue Apr  4 08:49:38 2023
    --- data/git/Example2.java	Tue Apr  4 08:49:38 2023
    ***************
    *** 1,6 ****
      public class Example {
    !   public boolean foo(int x, int y) {
    !     if(x < y)
            return true;
      
          return false;
    --- 1,6 ----
      public class Example {
    !   public boolean foo(int v, int w) {
    !     if(v < w)
            return true;
      
          return false;





```bash
diff -c data/git/Example1.java data/git/Example3.java
```

    *** data/git/Example1.java	Tue Apr  4 08:49:38 2023
    --- data/git/Example3.java	Tue Apr  4 08:49:38 2023
    ***************
    *** 3,8 ****
    --- 3,11 ----
          if(x < y)
            return true;
      
    +     if(x == y)
    +       return true;
    + 
          return false;
        }
      }





```bash
diff -c data/git/Example1.java data/git/Example4.java
```

    *** data/git/Example1.java	Tue Apr  4 08:49:38 2023
    --- data/git/Example4.java	Tue Apr  4 08:49:38 2023
    ***************
    *** 1,7 ****
      public class Example {
        public boolean foo(int x, int y) {
    -     if(x < y)
    -       return true;
      
          return false;
        }
    --- 1,5 ----




### Unified diff format

Als Austauschformat wird das _Unified_-Diff-Format verwendet


```bash
diff -u data/git/Example1.java data/git/Example2.java
```

    --- data/git/Example1.java	2023-04-04 08:49:38
    +++ data/git/Example2.java	2023-04-04 08:49:38
    @@ -1,6 +1,6 @@
     public class Example {
    -  public boolean foo(int x, int y) {
    -    if(x < y)
    +  public boolean foo(int v, int w) {
    +    if(v < w)
           return true;
     
         return false;




Zeilen mit Präfix `+` wurden hinzugefügt, mit Präfix `-` entfernt.


```bash
diff -u data/git/Example1.java data/git/Example3.java
```

    --- data/git/Example1.java	2023-04-04 08:49:38
    +++ data/git/Example3.java	2023-04-04 08:49:38
    @@ -3,6 +3,9 @@
         if(x < y)
           return true;
     
    +    if(x == y)
    +      return true;
    +
         return false;
       }
     }





```bash
diff -u data/git/Example1.java data/git/Example4.java
```

    --- data/git/Example1.java	2023-04-04 08:49:38
    +++ data/git/Example4.java	2023-04-04 08:49:38
    @@ -1,7 +1,5 @@
     public class Example {
       public boolean foo(int x, int y) {
    -    if(x < y)
    -      return true;
     
         return false;
       }




## Patch

Das Unified Diff Format erlaubt es, bestehende Dateien anzupassen. Der Unterschied wird dazu in einem *Patch* gespeichert.


```bash
diff -u data/git/Example1.java data/git/Example2.java > patch.txt
```




```bash
cat patch.txt
```

    --- data/git/Example1.java	2023-04-04 08:49:38
    +++ data/git/Example2.java	2023-04-04 08:49:38
    @@ -1,6 +1,6 @@
     public class Example {
    -  public boolean foo(int x, int y) {
    -    if(x < y)
    +  public boolean foo(int v, int w) {
    +    if(v < w)
           return true;
     
         return false;



```bash
cp data/git/Example1.java Example.java
```


```bash
cat Example.java
```

    public class Example {
      public boolean foo(int x, int y) {
        if(x < y)
          return true;
    
        return false;
      }
    }


Um die Datei `Example.java` nun basierend auf unserem Patch zu verändern, benötigen wir das Kommando `patch`.


```bash
patch -p0 Example.java < patch.txt
```

    patching file Example.java



```bash
cat Example.java
```

    public class Example {
      public boolean foo(int v, int w) {
        if(v < w)
          return true;
    
        return false;
      }
    }



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

    Initialized empty Git repository in /Users/gordon/Documents/Notebooks/se2023/tmp/dev1/.git/


Um Veränderungen im Workspace zu simulieren, fügen wir eine Datei `hello.txt` hinzu.


```bash
echo "Hallo SE 2023" > hello.txt
```


```bash
ls
```

    hello.txt


Um diese neue Datei in die Versionsverwaltung aufzunehmen, dient das `add` Kommando in Git.


```bash
git add hello.txt
```

Damit befindet sich die Datei auf der "Stage". Um sie ins Repository einzuchecken, dient das `commit` Kommando.


```bash
git commit -m "Add hello"
```

    [main (root-commit) b22f05b] Add hello
     1 file changed, 1 insertion(+)
     create mode 100644 hello.txt


Git-Output wird mit einem "Pager" angezeigt, der per Default auf einen Tastendruck wartet. Nachdem das im Jupyter Notebook nicht geht, müssen wir den an dieser Stelle umkonfigurieren. Das ist normalerweise nicht notwendig.


```bash
# Only required for presenting in the Jupyter notebook
export GIT_PAGER=cat
```

Die Historie unserers Repositories können wir mit `log` ansehen.


```bash
git log
```

    [33mcommit b22f05b1855466d5b239228ea5fc8a2bbdedc274[m[33m ([m[1;36mHEAD -> [m[1;32mmain[m[33m)[m
    Author: Gordon Fraser <gordon.fraser@uni-passau.de>
    Date:   Mon May 8 16:50:54 2023 +0200
    
        Add hello


Nun verändern wir die Datei nochmal (und simulieren damit, dass Entwickler 1 arbeitet).


```bash
echo "Hello again" >> hello.txt
```

Den Status unseres Workspaces können wir mit `status` einsehen.


```bash
git status
```

    On branch main
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git restore <file>..." to discard changes in working directory)
    	[31mmodified:   hello.txt[m
    
    no changes added to commit (use "git add" and/or "git commit -a")


Wenn wir wissen wollen, was verändert wurde, können wir uns einen Diff anzeigen lassen.


```bash
git diff
```

    [1mdiff --git a/hello.txt b/hello.txt[m
    [1mindex 7712a35..26921e3 100644[m
    [1m--- a/hello.txt[m
    [1m+++ b/hello.txt[m
    [36m@@ -1 +1,2 @@[m
     Hallo SE 2023[m
    [32m+[m[32mHello again[m


Das Committen der neuen Änderungen funktioniert wieder ähnlich.


```bash
git add hello.txt
```


```bash
git commit -m "Updated hello"
```

    [main 4b76c11] Updated hello
     1 file changed, 1 insertion(+)


Die Historie zeigt uns nun zwei Änderungen.


```bash
git log
```

    [33mcommit 4b76c119d914beadf95a6959bdb133177d758d96[m[33m ([m[1;36mHEAD -> [m[1;32mmain[m[33m)[m
    Author: Gordon Fraser <gordon.fraser@uni-passau.de>
    Date:   Mon May 8 16:50:56 2023 +0200
    
        Updated hello
    
    [33mcommit b22f05b1855466d5b239228ea5fc8a2bbdedc274[m
    Author: Gordon Fraser <gordon.fraser@uni-passau.de>
    Date:   Mon May 8 16:50:54 2023 +0200
    
        Add hello


Wenn wir wissen wollen, wer zuletzt die Datei `hello.txt` geändert hat, dann finden wir das mit `blame` heraus.


```bash
git blame hello.txt
```

    ^b22f05b (Gordon Fraser 2023-05-08 16:50:54 +0200 1) Hallo SE 2023
    4b76c119 (Gordon Fraser 2023-05-08 16:50:56 +0200 2) Hello again


### Nicht-committete Änderungen rückgängig machen

Angenommen wir nehmen wieder eine lokale Änderung vor.


```bash
echo "Hello yet again" >> hello.txt
```


```bash
git status
```

    On branch main
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git restore <file>..." to discard changes in working directory)
    	[31mmodified:   hello.txt[m
    
    no changes added to commit (use "git add" and/or "git commit -a")


Wenn wir lokale Änderungen, die noch nicht "staged" oder "committed" sind, rückgängig machen wollen, dann können wir dazu einfach die Version in `HEAD` auschecken.


```bash
git checkout hello.txt
```

    Updated 1 path from the index



```bash
git status
```

    On branch main
    nothing to commit, working tree clean



```bash
cat hello.txt
```

    Hallo SE 2023
    Hello again


### Unstaging

Nehmen wir noch eine Änderung vor:


```bash
echo "Hello yet again" >> hello.txt
git add hello.txt
```


```bash
git status
```

    On branch main
    Changes to be committed:
      (use "git restore --staged <file>..." to unstage)
    	[32mmodified:   hello.txt[m
    


Diese Änderung ist nun schon "staged". Wenn wir diese Änderung rückgängig machen wollen, reicht ein "checkout" nicht aus:


```bash
git checkout hello.txt
```

    Updated 0 paths from the index



```bash
git status
```

    On branch main
    Changes to be committed:
      (use "git restore --staged <file>..." to unstage)
    	[32mmodified:   hello.txt[m
    


Zuvor müssen wir die Datei "unstagen":


```bash
git reset hello.txt
```

    Unstaged changes after reset:
    M	hello.txt



```bash
git status
```

    On branch main
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git restore <file>..." to discard changes in working directory)
    	[31mmodified:   hello.txt[m
    
    no changes added to commit (use "git add" and/or "git commit -a")


Aber committen wir die Änderung zunächst doch noch.


```bash
git add hello.txt
git commit -m "Hello the third"
```

    [main e0f4538] Hello the third
     1 file changed, 1 insertion(+)


### Reverting

Wir können eine beliebige Version der Datei `hello.txt` auschecken. Die folgenden Commits existieren:


```bash
git log
```

    [33mcommit e0f45388beb76b58a5eb677ac2f2cbac0297f49e[m[33m ([m[1;36mHEAD -> [m[1;32mmain[m[33m)[m
    Author: Gordon Fraser <gordon.fraser@uni-passau.de>
    Date:   Mon May 8 16:51:00 2023 +0200
    
        Hello the third
    
    [33mcommit 4b76c119d914beadf95a6959bdb133177d758d96[m
    Author: Gordon Fraser <gordon.fraser@uni-passau.de>
    Date:   Mon May 8 16:50:56 2023 +0200
    
        Updated hello
    
    [33mcommit b22f05b1855466d5b239228ea5fc8a2bbdedc274[m
    Author: Gordon Fraser <gordon.fraser@uni-passau.de>
    Date:   Mon May 8 16:50:54 2023 +0200
    
        Add hello


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

    On branch main
    Changes to be committed:
      (use "git restore --staged <file>..." to unstage)
    	[32mmodified:   hello.txt[m
    



```bash
cat hello.txt
```

    Hallo SE 2023


Die Datei zählt nun als geändert, wenn wir diese Änderung behalten wollen (und damit die letzten beiden Commits rückgängig machen), dann müssen wir sie commiten.


```bash
git add hello.txt
git commit -m "Reverted to original version"
```

    [main ddb8690] Reverted to original version
     1 file changed, 2 deletions(-)



```bash
git log hello.txt
```

    [33mcommit ddb8690621c861ad554a8a0c54ba1d3b55045798[m[33m ([m[1;36mHEAD -> [m[1;32mmain[m[33m)[m
    Author: Gordon Fraser <gordon.fraser@uni-passau.de>
    Date:   Mon May 8 16:51:02 2023 +0200
    
        Reverted to original version
    
    [33mcommit e0f45388beb76b58a5eb677ac2f2cbac0297f49e[m
    Author: Gordon Fraser <gordon.fraser@uni-passau.de>
    Date:   Mon May 8 16:51:00 2023 +0200
    
        Hello the third
    
    [33mcommit 4b76c119d914beadf95a6959bdb133177d758d96[m
    Author: Gordon Fraser <gordon.fraser@uni-passau.de>
    Date:   Mon May 8 16:50:56 2023 +0200
    
        Updated hello
    
    [33mcommit b22f05b1855466d5b239228ea5fc8a2bbdedc274[m
    Author: Gordon Fraser <gordon.fraser@uni-passau.de>
    Date:   Mon May 8 16:50:54 2023 +0200
    
        Add hello


### Detached Head

Vorsichtig muss man sein, wenn man nicht nur eine Datei in einer alten Version auscheckt, sondern den ganzen Workspace in den Zustand versetzt.


```bash
git checkout $COMMIT
```

    Note: switching to 'b22f05b'.
    
    You are in 'detached HEAD' state. You can look around, make experimental
    changes and commit them, and you can discard any commits you make in this
    state without impacting any branches by switching back to a branch.
    
    If you want to create a new branch to retain commits you create, you may
    do so (now or later) by using -c with the switch command. Example:
    
      git switch -c <new-branch-name>
    
    Or undo this operation with:
    
      git switch -
    
    Turn off this advice by setting config variable advice.detachedHead to false
    
    HEAD is now at b22f05b Add hello


Wenn wir uns die Historie ansehen, sind die letzten beiden Commits nun verschwunden:


```bash
git log --oneline
```

    [33mb22f05b[m[33m ([m[1;36mHEAD[m[33m)[m Add hello


Noch ist nichts verloren, wir können einfach wieder die "main" Version auschecken.


```bash
git checkout main
```

    Previous HEAD position was b22f05b Add hello
    Switched to branch 'main'



```bash
git log --oneline
```

    [33mddb8690[m[33m ([m[1;36mHEAD -> [m[1;32mmain[m[33m)[m Reverted to original version
    [33me0f4538[m Hello the third
    [33m4b76c11[m Updated hello
    [33mb22f05b[m Add hello


`main` ist ein Branch. Um zu sehen, wie wir dieses Problem umgehen können, schauen wir uns zunächst wie man mit Branches arbeitet.

## Branches

Zwischen Branches wechselt man mit dem Kommando `checkout`; einen neuen Branch erstellt man mit `checkout -b`.


```bash
git checkout -b se2
```

    Switched to a new branch 'se2'


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

    [se2 d670511] Add branch hello
     1 file changed, 1 insertion(+)



```bash
git log --oneline
```

    [33md670511[m[33m ([m[1;36mHEAD -> [m[1;32mse2[m[33m)[m Add branch hello
    [33mddb8690[m[33m ([m[1;32mmain[m[33m)[m Reverted to original version
    [33me0f4538[m Hello the third
    [33m4b76c11[m Updated hello
    [33mb22f05b[m Add hello


Wenn wir zurück in den `main` Branch wechseln, dann ist die letzte Änderung hier nicht vorhanden.


```bash
git checkout main
```

    Switched to branch 'main'



```bash
cat hello.txt
```

    Hallo SE 2023



```bash
git log --oneline
```

    [33mddb8690[m[33m ([m[1;36mHEAD -> [m[1;32mmain[m[33m)[m Reverted to original version
    [33me0f4538[m Hello the third
    [33m4b76c11[m Updated hello
    [33mb22f05b[m Add hello


Um Branches zusammenzuführen, dient das Kommando "merge". Mergen wir also `se2` nach `main`:


```bash
git merge se2
```

    Updating ddb8690..d670511
    Fast-forward
     hello.txt | 1 [32m+[m
     1 file changed, 1 insertion(+)


Nun sind die Änderungen wieder zusammengeführt.


```bash
cat hello.txt
```

    Hallo SE 2023
    Hello in the se2 branch


Änderungen in main sind in `se2` nicht sichtbar:


```bash
echo "Hello added in main" >> hello.txt
git add hello.txt
git commit -m "Added hello main"
```

    [main 6724f10] Added hello main
     1 file changed, 1 insertion(+)



```bash
git checkout se2
```

    Switched to branch 'se2'



```bash
cat hello.txt
```

    Hallo SE 2023
    Hello in the se2 branch


Wir führen nun auch in `se2` eine Änderung durch und committen.


```bash
echo "Hello added again in se2" >> hello.txt
git add hello.txt
git commit -m "Added hello to se2 again"
```

    [se2 e1e1220] Added hello to se2 again
     1 file changed, 1 insertion(+)


Diese Änderung ist natürlich in `main` nicht sichtbar.


```bash
git checkout main
```

    Switched to branch 'main'



```bash
cat hello.txt
```

    Hallo SE 2023
    Hello in the se2 branch
    Hello added in main


Wenn wir nun allerdings einen Merge probieren, gibt es ein Problem: Die Datei `hello.txt` wurde in beiden Branches editiert.


```bash
git merge se2
```

    Auto-merging hello.txt
    CONFLICT (content): Merge conflict in hello.txt
    Automatic merge failed; fix conflicts and then commit the result.




Der Merge-Conflict wird in der Datei angezeigt.


```bash
cat hello.txt
```

    Hallo SE 2023
    Hello in the se2 branch
    <<<<<<< HEAD
    Hello added in main
    =======
    Hello added again in se2
    >>>>>>> se2



```bash
git status
```

    On branch main
    You have unmerged paths.
      (fix conflicts and run "git commit")
      (use "git merge --abort" to abort the merge)
    
    Unmerged paths:
      (use "git add <file>..." to mark resolution)
    	[31mboth modified:   hello.txt[m
    
    no changes added to commit (use "git add" and/or "git commit -a")


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

    Hallo SE 2022
    Hello in the se2 branch
    Hello added in main
    Hello added again in se2


Wir befinden uns noch im Merge, und müssen diesen abschliessen indem wir die Datei, in der wir den Merge-Conflict behoben haben, stagen und dann committen.


```bash
git status
```

    On branch main
    You have unmerged paths.
      (fix conflicts and run "git commit")
      (use "git merge --abort" to abort the merge)
    
    Unmerged paths:
      (use "git add <file>..." to mark resolution)
    	[31mboth modified:   hello.txt[m
    
    no changes added to commit (use "git add" and/or "git commit -a")



```bash
git add hello.txt
```

Ein Merge-Commit braucht keine explizite Commit-Message.


```bash
git commit --no-edit
```

    [main bedf338] Merge branch 'se2'



```bash
git log --oneline
```

    [33mbedf338[m[33m ([m[1;36mHEAD -> [m[1;32mmain[m[33m)[m Merge branch 'se2'
    [33me1e1220[m[33m ([m[1;32mse2[m[33m)[m Added hello to se2 again
    [33m6724f10[m Added hello main
    [33md670511[m Add branch hello
    [33mddb8690[m Reverted to original version
    [33me0f4538[m Hello the third
    [33m4b76c11[m Updated hello
    [33mb22f05b[m Add hello


Wir können uns hierzu auch einen Commit-Graphen anzeigen lassen.


```bash
git log --oneline --graph
```

    *   [33mbedf338[m[33m ([m[1;36mHEAD -> [m[1;32mmain[m[33m)[m Merge branch 'se2'
    [31m|[m[32m\[m  
    [31m|[m * [33me1e1220[m[33m ([m[1;32mse2[m[33m)[m Added hello to se2 again
    * [32m|[m [33m6724f10[m Added hello main
    [32m|[m[32m/[m  
    * [33md670511[m Add branch hello
    * [33mddb8690[m Reverted to original version
    * [33me0f4538[m Hello the third
    * [33m4b76c11[m Updated hello
    * [33mb22f05b[m Add hello


### Detached Head reparieren

Erstellen wir nochmal den problematischen Detached Head:


```bash
# Checkout first commit
git checkout $COMMIT
```

    Note: switching to 'b22f05b'.
    
    You are in 'detached HEAD' state. You can look around, make experimental
    changes and commit them, and you can discard any commits you make in this
    state without impacting any branches by switching back to a branch.
    
    If you want to create a new branch to retain commits you create, you may
    do so (now or later) by using -c with the switch command. Example:
    
      git switch -c <new-branch-name>
    
    Or undo this operation with:
    
      git switch -
    
    Turn off this advice by setting config variable advice.detachedHead to false
    
    HEAD is now at b22f05b Add hello


Die Lösung des Problems liegt darin, für den aktuellen Zustand einen neuen Branch anzulegen.


```bash
git checkout -b new_feature
```

    Switched to a new branch 'new_feature'



```bash
echo "Adding hello to initial hello" >> hello.txt
```


```bash
git add hello.txt
```


```bash
git commit -m "Added hello in branch"
```

    [new_feature 35d70ad] Added hello in branch
     1 file changed, 1 insertion(+)



```bash
git log --oneline --graph
```

    * [33m35d70ad[m[33m ([m[1;36mHEAD -> [m[1;32mnew_feature[m[33m)[m Added hello in branch
    * [33mb22f05b[m Add hello


Diese Änderung ist natürlich noch nicht im Main-Branch:


```bash
git checkout main
```

    Switched to branch 'main'



```bash
cat hello.txt
```

    Hallo SE 2022
    Hello in the se2 branch
    Hello added in main
    Hello added again in se2


## Rebase

Ein Merge erzeugt einen expliziten Merge-Commit und eine Verzweigung in der Commit-Historie. Eine Alternative dazu ist ein Rebase, welches die Commits eines Branches auf einen alternativen Startpunkt anwendet. Wir erzeugen uns dazu wieder einen Branch der von dem ersten Commit verzweigt.


```bash
git checkout $COMMIT
```

    Note: switching to 'b22f05b'.
    
    You are in 'detached HEAD' state. You can look around, make experimental
    changes and commit them, and you can discard any commits you make in this
    state without impacting any branches by switching back to a branch.
    
    If you want to create a new branch to retain commits you create, you may
    do so (now or later) by using -c with the switch command. Example:
    
      git switch -c <new-branch-name>
    
    Or undo this operation with:
    
      git switch -
    
    Turn off this advice by setting config variable advice.detachedHead to false
    
    HEAD is now at b22f05b Add hello



```bash
git checkout -b rebase_feature
```

    Switched to a new branch 'rebase_feature'


In diesem Branch erzeugen wir eine neue Datei `hello2.txt`.


```bash
echo "Hello again" > hello2.txt
```


```bash
git add hello2.txt
git commit -m "Add new hello file"
```

    [rebase_feature c9877c4] Add new hello file
     1 file changed, 1 insertion(+)
     create mode 100644 hello2.txt


In diesem Branch fehlen uns nun aber die ganzen Änderungen, die an der Datei `hello.txt` angwendet wurden:


```bash
cat hello.txt
```

    Hallo SE 2023



```bash
git log --oneline --graph
```

    * [33mc9877c4[m[33m ([m[1;36mHEAD -> [m[1;32mrebase_feature[m[33m)[m Add new hello file
    * [33mb22f05b[m Add hello


Wir können den Branch `rebase_feature` aber mit dem aktuellen Main rebasen:


```bash
git rebase main
```

    Rebasing (1/1)[KSuccessfully rebased and updated refs/heads/rebase_feature.


Hierdurch wird der Commit, mit dem `hello2.txt` erstellt wurde, auf den Main angewendet.


```bash
cat hello.txt
```

    Hallo SE 2022
    Hello in the se2 branch
    Hello added in main
    Hello added again in se2



```bash
git log --oneline --graph
```

    * [33mdfba641[m[33m ([m[1;36mHEAD -> [m[1;32mrebase_feature[m[33m)[m Add new hello file
    *   [33mbedf338[m[33m ([m[1;32mmain[m[33m)[m Merge branch 'se2'
    [32m|[m[33m\[m  
    [32m|[m * [33me1e1220[m[33m ([m[1;32mse2[m[33m)[m Added hello to se2 again
    * [33m|[m [33m6724f10[m Added hello main
    [33m|[m[33m/[m  
    * [33md670511[m Add branch hello
    * [33mddb8690[m Reverted to original version
    * [33me0f4538[m Hello the third
    * [33m4b76c11[m Updated hello
    * [33mb22f05b[m Add hello


Die Datei `hello2.txt` existiert aber noch nicht im Main:


```bash
git checkout main
```

    Switched to branch 'main'



```bash
ls
```

    hello.txt


Wir müssen den Branch dazu nach Main mergen.


```bash
git merge rebase_feature
```

    Updating bedf338..dfba641
    Fast-forward
     hello2.txt | 1 [32m+[m
     1 file changed, 1 insertion(+)
     create mode 100644 hello2.txt



```bash
ls
```

    hello.txt  hello2.txt


Nachdem der Branch `rebase_feature` auf Main rebased war, wird ein Fast-Forward-Merge angewendet, d.h. es benötigt keinen eigenen Merge-Commit.


```bash
git log --oneline --graph
```

    * [33mdfba641[m[33m ([m[1;36mHEAD -> [m[1;32mmain[m[33m, [m[1;32mrebase_feature[m[33m)[m Add new hello file
    *   [33mbedf338[m Merge branch 'se2'
    [32m|[m[33m\[m  
    [32m|[m * [33me1e1220[m[33m ([m[1;32mse2[m[33m)[m Added hello to se2 again
    * [33m|[m [33m6724f10[m Added hello main
    [33m|[m[33m/[m  
    * [33md670511[m Add branch hello
    * [33mddb8690[m Reverted to original version
    * [33me0f4538[m Hello the third
    * [33m4b76c11[m Updated hello
    * [33mb22f05b[m Add hello


## Stashing

Es kann vorkommen, dass man Änderungen von anderer Stelle einpflegen muss, bevor das Feature, an dem man gerade arbeitet, fertig ist, sodass man es noch nicht committen will. Man kann die Änderungen, die noch nicht committed sind, auf einen "Stash" schieben.


```bash
echo "Some unfinished change" >> hello.txt
```


```bash
cat hello.txt
```

    Hallo SE 2022
    Hello in the se2 branch
    Hello added in main
    Hello added again in se2
    Some unfinished change



```bash
git status
```

    On branch main
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git restore <file>..." to discard changes in working directory)
    	[31mmodified:   hello.txt[m
    
    no changes added to commit (use "git add" and/or "git commit -a")



```bash
git stash
```

    Saved working directory and index state WIP on main: dfba641 Add new hello file


Damit sind nun alle Dateien wieder am gleichen Stand wie `master`.


```bash
git status
```

    On branch main
    nothing to commit, working tree clean



```bash
cat hello.txt
```

    Hallo SE 2022
    Hello in the se2 branch
    Hello added in main
    Hello added again in se2


Um die lokalen Änderungen wieder einzufügen, verwendet man `stash apply`:


```bash
git stash apply
```

    On branch main
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git restore <file>..." to discard changes in working directory)
    	[31mmodified:   hello.txt[m
    
    no changes added to commit (use "git add" and/or "git commit -a")



```bash
cat hello.txt
```

    Hallo SE 2022
    Hello in the se2 branch
    Hello added in main
    Hello added again in se2
    Some unfinished change



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

    Initialized empty Git repository in /Users/gordon/Documents/Notebooks/se2023/tmp/remote_repo/


Entwickler 1 und Entwickler 2 können sich Klone des Repositories mit dem "clone" Befehl erstellen. (Der Befehl wird aktuell noch meckern dass das Repository leer ist).


```bash
cd ..
```


```bash
git clone remote_repo dev1
```

    Cloning into 'dev1'...
    warning: You appear to have cloned an empty repository.
    done.



```bash
git clone remote_repo dev2
```

    Cloning into 'dev2'...
    warning: You appear to have cloned an empty repository.
    done.


Zunächst simulieren wir, dass Entwickler 1 in seinem Workspace arbeitet.


```bash
cd dev1
```


```bash
echo "I am developer 1" >> hello.txt
git add hello.txt
git commit -m "Hello 1"
```

    [main (root-commit) 92e599e] Hello 1
     1 file changed, 1 insertion(+)
     create mode 100644 hello.txt


Um die Änderungen im eigenen Repository an das "Remote" Repository zu schieben, dient der "push" Befehl:


```bash
git push
```

    Enumerating objects: 3, done.
    Counting objects: 100% (3/3), done.
    Writing objects: 100% (3/3), 233 bytes | 233.00 KiB/s, done.
    Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
    To /Users/gordon/Documents/Notebooks/se2023/tmp/remote_repo
     * [new branch]      main -> main


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

    remote: Enumerating objects: 3, done.[K
    remote: Counting objects: 100% (3/3), done.[K
    remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0[K
    Unpacking objects: 100% (3/3), 213 bytes | 106.00 KiB/s, done.
    From /Users/gordon/Documents/Notebooks/se2023/tmp/remote_repo
     * [new branch]      main       -> origin/main



```bash
cat hello.txt
```

    I am developer 1


Nun arbeitet auch Entwickler 2 im eigenen Workspace.


```bash
echo "I am developer 2" >> hello.txt
git add hello.txt
git commit -m "Add hello from developer 2"
```

    [main 0a3d433] Add hello from developer 2
     1 file changed, 1 insertion(+)


Die Änderungen können per "push" wieder geteilt werden.


```bash
git push
```

    Enumerating objects: 5, done.
    Counting objects: 100% (5/5), done.
    Writing objects: 100% (3/3), 278 bytes | 278.00 KiB/s, done.
    Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
    To /Users/gordon/Documents/Notebooks/se2023/tmp/remote_repo
       92e599e..0a3d433  main -> main


Entwickler 1 kann sich diese Änderungen mit "pull" holen.


```bash
cd ..
cd dev1
```


```bash
cat hello.txt
```

    I am developer 1



```bash
git pull
```

    remote: Enumerating objects: 5, done.[K
    remote: Counting objects: 100% (5/5), done.[K
    remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0[K
    Unpacking objects: 100% (3/3), 258 bytes | 129.00 KiB/s, done.
    From /Users/gordon/Documents/Notebooks/se2023/tmp/remote_repo
       92e599e..0a3d433  main       -> origin/main
    Updating 92e599e..0a3d433
    Fast-forward
     hello.txt | 1 [32m+[m
     1 file changed, 1 insertion(+)



```bash
git log
```

    [33mcommit 0a3d43357d2b56f9358adcaa19557d37127aead3[m[33m ([m[1;36mHEAD -> [m[1;32mmain[m[33m, [m[1;31morigin/main[m[33m)[m
    Author: Gordon Fraser <gordon.fraser@uni-passau.de>
    Date:   Mon May 8 16:51:28 2023 +0200
    
        Add hello from developer 2
    
    [33mcommit 92e599e192e9ab814a2f8bae83aeec72f816c6ea[m
    Author: Gordon Fraser <gordon.fraser@uni-passau.de>
    Date:   Mon May 8 16:51:24 2023 +0200
    
        Hello 1



```bash
cat hello.txt
```

    I am developer 1
    I am developer 2



```bash
cd ..
```

## Remotes

Ein Git Repository ist nicht an ein einzelnes Remote-Repository gebunden, sondern kann sich mit beliebig vielen anderen Repositories austauschen. Klonen wir zunächst ein Repository von GitHub:


```bash
git clone https://github.com/se2p/se2022-gitexample.git
```

    Cloning into 'se2022-gitexample'...
    warning: You appear to have cloned an empty repository.



```bash
cd se2022-gitexample
```

Wenn ein Repository per "clone" erstellt wird, dann heisst das Upstream-Repository `origin`. Wir können uns ansehen, wo `origin` liegt:


```bash
git remote get-url origin
```

    https://github.com/se2p/se2022-gitexample.git


Legen wir uns noch einen Klon des neuen Repositories an:


```bash
cd ..
git clone se2022-gitexample another_clone
```

    Cloning into 'another_clone'...
    warning: You appear to have cloned an empty repository.
    done.



```bash
cd another_clone
```

`origin` ist nun einfach das Verzeichnis unseres lokalen Git-Repositories, das wir eben geklont haben:


```bash
git remote get-url origin
```

    /Users/gordon/Documents/Notebooks/se2023/tmp/se2022-gitexample


Wir aber nun auch das GitHub-Repository als Remote hinzufügen:


```bash
git remote add github https://github.com/se2p/se2022-gitexample.git
```

Unser Repository hat nun zwei Remotes:


```bash
git remote
```

    github
    origin


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

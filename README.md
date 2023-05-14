# Software Engineering SS2023

In diesem Repository werden die Beispiele aus der Vorlesung gesammelt. Um
die Inhalte einfacher zugaenglich zu machen, werden sie dazu in [Jupyter
Notebooks](https://jupyter.org/) verpackt. Es wird nicht zu jeder Vorlesung
ein eigenes Notebook geben, sondern nur dort wo tatsaechlich benoetigt.


## Installation

Die fertig ausgefuehrten Notebooks werden in exportierter Form (PDF) auf
StudIP hochgeladen. Wer selber das Notebook ausfuehren und veraendern will,
wird dazu Jupyter benoetigen; siehe
[Installationsanleitung](https://jupyter.org/install).

Um Notebooks mit Java Beispielen auszufuehren wird ein IJava Kernel
benoetigt: [https://github.com/SpencerPark/IJava](https://github.com/SpencerPark/IJava)

UML Diagramme werden mit [PlantUML](https://plantuml.com/) erzeugt. In
Java-Notebooks werden Diagramme als externe Dateien eingebunden, und die
PlantUML Sourcen befinden sich in diesem Repository.

Eigene Notebooks zu UML werden die IPlantUML Erweiterung benoetigen:
[https://github.com/jbn/IPlantUML](https://github.com/jbn/IPlantUML)


## Inhalt

### 1: Vorlesung vom 17.4.2023: Einfuehrung, Recap OOP

Auch wenn diese Vorlesung keine Programmiervorlesung ist, so benoetigen wir
dennoch ein gewisses Verstaendnis elementarer OOP Konzepte. Das erste Notebook
enthaelt einfache Beispiele dazu. [Markdown Export](rendered/1%20Einführung%20in%20OOP.md)




### 2: Vorlesung vom 24.4.2023: Versionsverwaltung mit Git

Softwareentwicklung ohne Versionsverwaltung ist undenkbar, und in dieser 
Vorlesung beschaeftigen wir uns mit dem verteilten Versionsverwaltungssystem
Git. Das Notebook betrachtet die Verwendung von Git an der Kommandozeile. [Markdown Export](rendered/2%20Versionskontrolle.md)

### 3: Vorlesung vom 8.5.2023: Testen mit JUnit

Das Testen ist eine der wichtigsten Methoden um die Qualitaet von Sourcecode
sicherzustellen. In diesem Notebook sind die Beispiele aus der Vorlesung
rund um das JUnit 4 Test-Framework fuer Java gesammelt. [Markdown Export](rendered/3%20Testen.md)

### 4: Vorlesung vom 15.5.2023: Refactoring

Refactoring bezeichnet den Vorgang der Verbesserung der Code Qualitaet, ohne
dabei die Funktionalitaet zu veraendern. Ein essentieller Bestandteil des
Refactorings ist daher, die bestehenden Tests regelmaessig auszufuehren und
die Funktionalitaet zu ueberpruefen. In diesem Notebook wird das in der
Vorlesung vorgestellte Code-Beispiel in Einzelschritten nochmal erklaert.
[Markdown Export](rendered/4%20Refactoring.md)

---
title: Dialoge innerhalb des Bot Framework SDK | Microsoft-Dokumentation
description: Hier wird beschrieben, was ein Dialog ist und wie er innerhalb des Bot Framework SDK funktioniert.
keywords: Konversationsablauf, Eingabeaufforderung, Dialogzustand, Erkennen der Absicht, Einzeldurchlauf, Mehrfachdurchlauf, Bot-Konversation, Dialoge, Eingabeaufforderungen, Wasserfälle, Dialogsatz
author: johnataylor
ms.author: johtaylo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/28/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: fc44701d7739ecfca662d27cad4f521caa7f4d6d
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54225485"
---
# <a name="dialogs-library"></a>Dialogbibliothek

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

*Dialoge* sind ein zentrales Konzept im SDK und eine praktische Möglichkeit zum Verwalten einer Konversation mit dem Benutzer. Dialoge sind Strukturen in Ihrem Bot, die sich wie Funktionen im Programm des Bots verhalten. Jeder Dialog führt eine spezifische Aufgabe in einer bestimmten Reihenfolge aus. Sie können die Reihenfolge der einzelnen Dialoge festlegen, um die Konversation zu steuern, und die Dialoge auf unterschiedliche Weise aufrufen, z. B. als Antwort für einen Benutzer, als Reaktion auf externe Bedingungen oder über andere Dialoge.

Die Dialogbibliothek enthält einige integrierte Funktionen wie *Eingabeaufforderungen* und *Wasserfalldialoge*, die die Verwaltung der Konversation Ihres Bots erleichtern. [Eingabeaufforderungen](#prompts) werden verwendet, um Benutzer zur Eingabe verschiedener Arten von Informationen aufzufordern, z. B. Text, eine Zahl oder ein Datum. [Wasserfalldialoge](#waterfall-dialogs) können mehrere Schritte in einer Sequenz kombinieren, sodass der Bot einfach dieser vordefinierten Sequenz folgen und Informationen an den nächsten Schritt übergeben kann.

<!-- When we have samples for building your own, add links and one liner about them -->

## <a name="dialogs-and-their-pieces"></a>Dialoge und ihre Bestandteile

Die Dialogbibliothek enthält einige zusätzliche Elemente, mit denen die Verwendbarkeit von Dialogen verbessert werden kann. Neben den verschiedenen [Dialogtypen](#dialog-types), die weiter unten erläutert werden, beinhaltet die Bibliothek das Konzept eines *Dialogsatzes*, des *Dialogkontexts* und des *Dialogergebnisses*.

Einfach ausgedrückt, ist ein *Dialogsatz* eine Sammlung von Dialogen. Dabei kann es sich beispielsweise um Eingabeaufforderungen, Wasserfalldialoge oder [Komponentendialoge](#component-dialog) handeln. All diese Elemente sind Implementierungen eines Dialogs, und jedes Element kann dem Dialogsatz mit einer bestimmten Zeichenfolgen-ID hinzugefügt werden. Um einen bestimmten Dialog oder eine Eingabeaufforderung im Dialogsatz zu starten, gibt der Bot anhand dieser Zeichenfolgen-ID an, welcher Dialog verwendet werden soll.

Der *Dialogkontext* enthält Informationen zum Dialog und wird verwendet, um innerhalb des Turn-Handlers des Bots mit einem Dialogsatz zu interagieren. Der Dialogkontext enthält den aktuellen Turn-Kontext, den übergeordneten Dialog und den [Dialogzustand](#dialog-state), der eine Methode zum Beibehalten von Informationen innerhalb des Dialogs bereitstellt. Der Dialogkontext ermöglicht es Ihnen, einen Dialog mit der zugehörigen Zeichenfolgen-ID zu starten oder den aktuellen Dialog fortzusetzen (z. B. einen Wasserfalldialog mit mehreren Schritten).

Wenn ein Dialog endet, kann er ein *Dialogergebnis* mit einigen resultierenden Informationen aus dem Dialog zurückgeben. Dieses Ergebnis wird zurückgegeben, um die aufrufende Methode darüber zu informieren, was im Dialog passiert ist, und die Informationen ggf. an einem permanenten Speicherort zu speichern.

## <a name="dialog-state"></a>Dialogzustand

Dialoge sind ein Ansatz zum Implementieren einer Konversation mit mehreren Durchläufen („Turns“) und ein Beispiel für ein Feature im SDK, das auf einem persistenten Zustand für mehrere Durchläufe beruht. Ohne Zustand in den Dialogen weiß Ihr Bot nicht, wo sich die Informationen im Dialogsatz befinden oder welche Informationen bereits erfasst wurden.

Ein auf Dialogen basierender Bot enthält normalerweise eine Dialogsatzsammlung als Membervariable in der Botimplementierung. Dieser Dialogsatz wird mit einem Handle für ein Objekt mit der Bezeichnung „Accessor“ erstellt, über das der Zugriff auf den persistenten Zustand möglich ist. Hintergrundinformationen zum Zustand in Bots finden Sie unter [Verwalten des Zustands](bot-builder-concept-state.md).

Innerhalb des Turn-Handlers des Bots initialisiert der Bot das Dialogsubsystem, indem er die *CreateContext*-Methode für den Dialogsatz aufruft, die einen *Dialogkontext* zurückgibt. Dieser Dialogkontext enthält die erforderlichen Informationen, die vom Dialog benötigt werden.

Für die Erstellung eines Dialogkontexts ist ein Zustand erforderlich, auf den mit dem Accessor zugegriffen wird. Der Accessor wird beim Erstellen des Dialogsatzes bereitgestellt. Mit diesem Accessor kann der Dialogsatz den entsprechenden Dialogzustand abrufen. Ausführlichere Informationen zu Zustandsaccessoren finden Sie unter [Speichern von Benutzer- und Konversationsdaten](bot-builder-howto-v4-state.md).

## <a name="dialog-types"></a>Dialogtypen

Wie in der unten stehenden Klassenhierarchie dargestellt, gibt es verschiedene Arten von Dialogen: Eingabeaufforderungen, Wasserfalldialoge und Komponentendialoge.

![Dialogklassen](media/bot-builder-dialog-classes.png)

### <a name="prompts"></a>Eingabeaufforderungen

Die in der Dialogbibliothek verfügbaren Eingabeaufforderungen sind eine einfache Möglichkeit, um Benutzer zur Eingabe von Informationen aufzufordern und ihre Antwort auszuwerten. Für eine *Zahleneingabeaufforderung* geben Sie beispielsweise die Frage oder die gewünschten Informationen an, und die Eingabeaufforderung überprüft automatisch, ob eine gültige Zahl als Antwort empfangen wurde. Wenn dies der Fall ist, kann die Konversation fortgesetzt werden. Andernfalls wird der Benutzer erneut zur Eingabe einer gültigen Antwort aufgefordert.

Im Hintergrund sind Eingabeaufforderungen ein aus zwei Schritten bestehender Dialog. Im ersten Schritt fordert die Eingabeaufforderung den Benutzer zur Eingabe auf. Im zweiten Schritt wird der gültige Wert zurückgegeben oder der Vorgang mit einer erneuten Eingabeaufforderung wiederholt.

Eingabeaufforderungen verfügen über *Eingabeaufforderungsoptionen*, die beim Aufrufen der Eingabeaufforderung bereitgestellt werden. In diesen Optionen können Sie den Text der Eingabeaufforderung, die erneute Eingabeaufforderung im Fall eines Validierungsfehlers und Auswahlmöglichkeiten für die Beantwortung der Eingabeaufforderung angeben.

Zudem können Sie beim Erstellen der Eingabeaufforderung eine benutzerdefinierte Validierung hinzufügen. Angenommen, Sie möchten mit der Zahleneingabeaufforderung eine Anzahl von Personen erfragen, diese Zahl muss jedoch größer als 2 und kleiner als 12 sein. Die Eingabeaufforderung überprüft zunächst, ob eine gültige Zahl empfangen wurde, und führt dann (sofern angegeben) die benutzerdefinierte Validierung aus. Falls die benutzerdefinierte Validierung fehlschlägt, wird der Benutzer wie oben beschrieben erneut zur Eingabe aufgefordert.

Eine Eingabeaufforderung gibt bei ihrem Abschluss den resultierenden angeforderten Wert explizit zurück. Wenn dieser Wert zurückgegeben wird, können wir sicher sein, dass er sowohl die integrierte Eingabeaufforderungsvalidierung als auch die zusätzliche benutzerdefinierte Validierung (sofern vorhanden) bestanden hat.

Beispiele zur Verwendung verschiedener Eingabeaufforderungen finden Sie im Artikel zum [Erfassen von Benutzereingaben mithilfe der Dialogbibliothek](bot-builder-prompts.md).

#### <a name="prompt-types"></a>Eingabeaufforderungstypen

Im Hintergrund sind Eingabeaufforderungen ein aus zwei Schritten bestehender Dialog. Im ersten Schritt fordert die Eingabeaufforderungen den Benutzer zur Eingabe auf. Im zweiten Schritt wird der gültige Wert zurückgegeben oder der Vorgang mit einer erneuten Eingabeaufforderung neu gestartet. Die Dialogebibliothek enthält viele einfache Eingabeaufforderungen, die jeweils zum Anfordern einer anderen Art von Antwort verwendet werden. Die einfachen Eingabeaufforderungen können Eingaben in natürlicher Sprache interpretieren (z. B. „zehn“ oder „ein Dutzend“ für eine Zahl oder „morgen“ oder „Freitag um 10 Uhr“ für eine Datums- und Zeitangabe).

| Prompt | BESCHREIBUNG | Rückgabe |
|:----|:----|:----|
| _Anlageneingabeaufforderung_ | Fordert den Benutzer auf, eine oder mehrere Anlagen anzufügen (beispielsweise ein Dokument oder Bild). | Eine Sammlung von _attachment_-Objekten. |
| _Auswahleingabeaufforderung_ | Fordert den Benutzer auf, eine Auswahl in einer Reihe von Optionen zu treffen. | Ein _found choice_-Objekt. |
| _Bestätigungseingabeaufforderung_ | Fordert den Benutzer zur Bestätigung auf. | Ein boolescher Wert. |
| _Datums-/Uhrzeiteingabeaufforderung_ | Fordert den Benutzer auf, ein Datum und eine Uhrzeit anzugeben. | Eine Sammlung von Objekten für die _Datums-/Uhrzeitauflösung_. |
| _Zahleneingabeaufforderung_ | Fordert den Benutzer auf, eine Zahl einzugeben. | Ein numerischer Wert. |
| _Texteingabeaufforderung_ | Fordert den Benutzer auf, einen allgemeinen Text einzugeben. | Eine Zeichenfolge. |

Um einen Benutzer zur Eingabe aufzufordern, definieren Sie eine Eingabeaufforderung mit einer der integrierten Klassen (beispielsweise _text prompt_), und fügen Sie diese dann Ihrem Dialogsatz hinzu. Eingabeaufforderungen verfügen über feste IDs, die innerhalb eines Dialogsatzes eindeutig sein müssen. Sie können ein benutzerdefiniertes Validierungssteuerelement für jede Eingabeaufforderung verwenden, und für einige Eingabeaufforderungen können Sie ein _Standardgebietsschema_ angeben. 

#### <a name="prompt-locale"></a>Gebietsschema der Eingabeaufforderung

Das Gebietsschema wird verwendet, um das sprachspezifische Verhalten der Eingabeaufforderungen **choice**, **confirm**, **date-time** und **number** zu bestimmen. Wenn für den Kanal eine _locale_-Eigenschaft angegeben wurde, wird für alle Eingaben des Benutzers dieser Wert verwendet. Wenn das _Standardgebietsschema_ der Eingabeaufforderung beim Aufrufen des Konstruktors der Eingabeaufforderung angegeben oder zu einem späteren Zeitpunkt festgelegt wird, wird andernfalls dieses Gebietsschema verwendet. Falls keine dieser Angaben vorhanden ist, wird Englisch („en-us“) als Gebietsschema genutzt. Hinweis: Das Gebietsschema ist ein zwei-, drei- oder vierstelliger ISO 639-Code, der eine Sprache oder eine Sprachfamilie darstellt.

### <a name="waterfall-dialogs"></a>Wasserfalldialoge

Ein Wasserfalldialog ist eine spezifische Implementierung eines Dialogs, die häufig verwendet wird, um Informationen vom Benutzer zu erfassen oder den Benutzer durch eine Reihe von Aufgaben zu leiten. Jeder Schritt der Konversation wird als eine asynchrone Funktion implementiert, die einen Parameter für den *Wasserfallschrittkontext* (`step`) akzeptiert. Bei jedem Schritt führt der Bot die folgenden Schritte aus: Er [fordert den Benutzer zur Eingabe auf](bot-builder-prompts.md) (oder startet einen untergeordneten Dialog, wobei es sich jedoch oft um eine Eingabeaufforderung handelt), wartet auf eine Antwort und übergibt das Ergebnis dann an den nächsten Schritt. Das Ergebnis der ersten Funktion wird als Argument an die nächste Funktion übergeben usw.

Das folgende Diagramm zeigt eine Sequenz von Wasserfallschritten und die ausgeführten Stapelvorgänge. Ausführliche Informationen zur Verwendung des Dialogstapels finden Sie weiter unten im Abschnitt [Verwenden von Dialogen](#using-dialogs).

![Dialogkonzept](media/bot-builder-dialog-concept.png)

In Wasserfallschritten wird der Kontext des Wasserfalldialogs im *Wasserfallschrittkontext* gespeichert. Dieser ähnelt dem Dialogkontext, da er Zugriff auf den aktuellen Turn-Kontext und Zustand bietet. Verwenden Sie das Wasserfallschritt-Kontextobjekt, um aus einem Wasserfallschritt heraus mit einem Dialogsatz zu interagieren.

Sie können den Rückgabewert eines Dialogs entweder innerhalb eines Wasserfallschritts in einem Dialog oder im Turn-Handler Ihres Bots verarbeiten. Im Allgemeinen müssen Sie jedoch nur den Status des Turn-Ergebnisses des Dialogs in der Turn-Logik Ihres Bots überprüfen.
In einem Wasserfallschritt stellt der Dialog den Rückgabewert in der _result_-Eigenschaft des Wasserfall-Schrittkontexts bereit.

#### <a name="waterfall-step-context-properties"></a>Eigenschaften des Wasserfallschrittkontexts

Der Wasserfallschrittkontext enthält Folgendes:

* *Options*: Eingabeinformationen für den Dialog.
* *Values*: Informationen, die dem Kontext hinzugefügt werden können und in nachfolgenden Schritten übernommen werden.
* *Result*: Das Ergebnis des vorherigen Schritts.

Zudem ist die *next*-Methode verfügbar, die den Dialog mit dem nächsten Schritt des Wasserfalldialogs innerhalb desselben Turns fortsetzt, sodass Ihr Bot bei Bedarf einen bestimmten Schritt überspringen kann.

### <a name="component-dialog"></a>Komponentendialog

Manchmal bietet es sich an, einen wiederverwendbaren Dialog zu schreiben, den Sie in verschiedenen Szenarien verwenden können (z. B. ein Adressendialog, bei dem der Benutzer zur Eingabe von Werten für Straße, Ort und Postleitzahl aufgefordert wird).

Der *Komponentendialog* ermöglicht das Erstellen unabhängiger Dialoge für bestimmte Szenarien, indem ein umfangreicher Dialogsatz in besser verwaltbare Teile unterteilt wird. Jeder dieser Teile verfügt über einen eigenen Dialogsatz, wodurch Namenskonflikte mit dem übergeordneten Dialogsatz vermieden werden. Weitere Informationen finden Sie in der [Anleitung für Komponentendialoge](bot-builder-compositcontrol.md).

## <a name="using-dialogs"></a>Verwenden von Dialogen

Der Dialogkontext kann zum Starten, Fortsetzen, Ersetzen oder Beenden eines Dialogs verwendet werden. Sie können auch alle Dialoge im Dialogstapel abbrechen.

Sie können sich Dialoge als einen programmgesteuerten Stapel vorstellen, den so genannten *Dialogstapel*. Der Turn-Handler steuert den Stapel und fungiert als Fallback, wenn der Stapel leer ist. Das oberste Element in diesem Stapel gilt als *aktiver Dialog*, und der Dialogkontext steuert sämtliche Eingaben für den aktiven Dialog.

Wenn ein Dialog gestartet wird, wird er in den Stapel gepusht und ist dann der aktive Dialog. Er bleibt der aktive Dialog, bis er beendet oder von der [ReplaceDialog](#repeating-a-dialog)-Methode entfernt wird oder bis ein anderer Dialog (vom Turn-Handler oder vom aktiven Dialog selbst) in den Stapel gepusht und zum aktiven Dialog wird. Wenn dieser neue Dialog endet, wird er per Pop aus dem Stapel entfernt, und der nächste Dialog im Stapel wird zum aktiven Dialog. Dies ermöglicht die unten erläuterten [Verzweigungen und Schleifen](#looping-and-branching).

### <a name="create-the-dialog-context"></a>Erstellen des Dialogkontexts

Um den Dialogkontext zu erstellen, rufen Sie die *CreateContext*-Methode Ihres Dialogsatzes auf. Die CreateContext-Methode ruft die *DialogState*-Eigenschaft des Dialogsatzes ab und erstellt anhand des abgerufenen Werts den Dialogkontext. Der Dialogkontext wird dann zum Starten, Fortsetzen oder anderweitigen Steuern der Dialoge im Satz verwendet.

Der Dialogsatz erfordert die Verwendung eines *Zustandseigenschaftenaccessors*, um auf den Dialogzustand zuzugreifen. Der Accessor wird wie andere Zustandsaccessoren erstellt und verwendet, er wird jedoch auf der Grundlage des Konversationszustands als seine eigene Eigenschaft erstellt. Informationen zur Verwaltung des Zustands finden Sie im Thema [Verwalten des Zustands](bot-builder-concept-state.md). Die Verwendung des Dialogzustands wird in der Anleitung [Implementieren eines sequenziellen Konversationsflusses](bot-builder-dialog-manage-conversation-flow.md) erläutert.

### <a name="to-start-a-dialog"></a>So starten Sie einen Dialog

Um einen Dialog zu starten, übergeben Sie die zu startende *Dialog-ID* an die Methode *BeginDialog*, *Prompt* oder *ReplaceDialog* des Dialogkontexts. Die BeginDialog-Methode verschiebt den Dialog an den Anfang des Stapels, während die ReplaceDialog-Methode den aktuellen Dialog per Pop aus dem Stapel entfernt und den Ersatzdialog in den Stapel verschiebt.

### <a name="to-continue-a-dialog"></a>So setzen Sie einen Dialog fort

Um einen Dialog fortzusetzen, rufen Sie die *ContinueDialog*-Methode auf. Die ContinueDialog-Methode setzt immer den obersten Dialog im Stapel (den aktiven Dialog) fort, sofern ein Dialog vorhanden ist. Wenn der fortgesetzte Dialog endet, wird die Steuerung an den übergeordneten Kontext übergeben, der die Ausführung innerhalb des gleichen Turns fortsetzt.

### <a name="to-end-a-dialog"></a>So beenden Sie einen Dialog

Die *EndDialog*-Methode beendet einen Dialog, indem sie ihn per Pop aus dem Stapel entfernt, und gibt ein optionales Ergebnis an den übergeordneten Kontext (z. B. den aufrufenden Dialog oder den Turn-Handler des Bots) zurück. Die Methode wird meistens innerhalb des Dialogs aufgerufen, um die aktuelle Instanz dieses Dialogs zu beenden.

Die EndDialog-Methode kann überall aufgerufen werden, wo ein Dialogkontext vorhanden ist, für den Bot entsteht jedoch der Eindruck, dass sie im aktuellen aktiven Dialog aufgerufen wurde.

> [!TIP]
> Die bewährte Methode besteht darin, am Ende des Dialogs explizit die *EndDialog*-Methode aufzurufen.

### <a name="to-clear-all-dialogs"></a>So löschen Sie alle Dialoge

Wenn Sie alle Dialoge aus dem Stapel entfernen möchten, können Sie den Dialogstapel löschen, indem Sie die *cancel all dialogs*-Methode des Dialogkontexts aufrufen.

### <a name="repeating-a-dialog"></a>Wiederholen eines Dialogs

Verwenden Sie zum Wiederholen eines Dialogs die *replace dialog*-Methode. Die *ReplaceDialog*-Methode des Dialogkontexts entfernt den aktuellen aktiven Dialog per Pop aus dem Stapel (ohne ihn normal zu beenden), verschiebt den ersetzenden Dialog an den Anfang des Stapels und startet diesen Dialog. Dies ist eine hervorragende Möglichkeit zum Behandeln [komplexer Interaktionen](~/v4sdk/bot-builder-dialog-manage-complex-conversation-flow.md) und eine gute Technik zum Verwalten von Menüs. Mit dieser Methode können Sie also Schleife erstellen, indem Sie einen Dialog durch sich selbst ersetzen.

> [!NOTE]
> Wenn der interne Zustand für den aktuellen Dialog beibehalten werden muss, müssen Sie im Aufruf der *ReplaceDialog*-Methode Informationen an die neue Instanz des Dialogs übergeben und dann den Dialog entsprechend initialisieren. Der Zugriff auf die in den neuen Dialog übergebenen Optionen ist in jedem Schritt des Dialogs über die *options*-Eigenschaft des Schrittkontexts möglich.

### <a name="branch-a-conversation"></a>Verzweigen einer Konversation

Der Dialogkontext verwaltet den Dialogstapel und verfolgt für jeden Dialog im Stapel nach, welcher Schritt als Nächstes ansteht. Die *BeginDialog*-Methode erstellt einen untergeordneten Dialog und pusht diesen Dialog an den Anfang des Stapels. Die *EndDialog*-Methode entfernt den obersten Dialog per Pop aus dem Stapel. *EndDialog* wird in der Regel innerhalb des Dialogs aufgerufen, der beendet wird.

Ein Dialog kann innerhalb des gleichen Dialogsatzes einen neuen Dialog starten. Dazu wird die Methode *begin dialog* des Dialogkontexts aufgerufen und die ID des neuen Dialogs angegeben. Dadurch wird der neue Dialog zum aktuell aktiven Dialog. Der ursprüngliche Dialog befindet sich zwar weiterhin im Stapel, Aufrufe der Methode *continue dialog* des Dialogkontexts werden aber nur an den Dialog gesendet, der sich ganz oben im Stapel befindet (*aktiver Dialog*). Wenn ein Dialog per Pop aus dem Stapel entfernt wird, fährt der Dialogkontext mit dem Schritt des Wasserfalls aus dem Stapel fort, der im ursprünglichen Dialog als nächstes an der Reihe wäre.

Somit können Sie eine Verzweigung innerhalb Ihres Konversationsablaufs erstellen, indem Sie einen Schritt in einen Dialog einschließen, der bei Bedarf einen Dialog aus einer Gruppe verfügbarer Dialoge startet.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erfassen von Benutzereingaben mit der Dialogebibliothek](bot-builder-prompts.md)

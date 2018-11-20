---
title: Dialoge im Bot Builder-SDK | Microsoft-Dokumentation
description: Beschreibt, was ein Dialog ist, und wie er innerhalb des Bot Builder-SDK funktioniert.
keywords: Konversationsablauf, Erkennen der Absicht, Einzeldurchlauf, Mehrfachdurchlauf, Bot-Konversation, Dialoge, Eingabeaufforderungen, Wasserfälle, Dialogsatz
author: johnataylor
ms.author: johtaylo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 9/22/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 88022c387d5f9ef7f645be74010aba3c676efadc
ms.sourcegitcommit: cb0b70d7cf1081b08eaf1fddb69f7db3b95b1b09
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2018
ms.locfileid: "51332934"
---
# <a name="dialogs-library"></a>Dialogbibliothek

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Die Verwaltung von Konversationen basierend auf dem Konzept eines Dialogs ist für das SDK von wesentlicher Bedeutung. Dialogobjekte verarbeiten eingehende Aktivitäten und generieren ausgehende Antworten. Die Geschäftslogik des Bots wird entweder direkt oder indirekt innerhalb von Dialogklassen ausgeführt.

Zur Laufzeit werden Dialoginstanzen in einem Stapel angeordnet. Der Dialog oben im Stapel wird als „ActiveDialog“ bezeichnet. Der aktuell aktive Dialog verarbeitet die eingehende Aktivität. Der Stapel wird zwischen jedem Sprecherwechsel der Konversation (die nicht zeitlich begrenzt ist und mehrere Tage dauern kann) beibehalten. 

## <a name="dialog-lifecycle"></a>Dialoglebenszyklus

Ein Dialogfeld implementiert drei Hauptfunktionen:
- BeginDialog
- ContinueDialog
- ResumeDialog

Zur Laufzeit wählen Dialoge und die DialogContext-Klassen gemeinsam den geeigneten Dialog für die Aktivität aus. Die DialogContext-Klasse verknüpft den beibehaltenen Dialogstapel, die eingehende Aktivität und die DialogSet-Klasse. Ein DialogSet-Element enthält Dialoge, die der Bot aufrufen kann.

Die Schnittstelle von DialogContext gibt die Auffassung von Beginn und Fortsetzung des Dialogs wieder. Das allgemeine Muster für die Anwendung sieht vor, dass ContinueDialog immer zuerst aufgerufen wird. Wenn kein Stapel und daher kein ActiveDialog-Element vorhanden sind, muss die Anwendung den gewählten Dialogfeld durch Aufrufen von „BeginDialog“ in „DialogContext“ beginnen. Dadurch wird der entsprechenden Dialogeintrag aus dem DialogSet-Element mithilfe von Push an den Stapel übertragen (genau genommen wird die Dialog-ID dem Stapel hinzugefügt), und dann wird ein Aufruf von „BeginDialog“ für das angegebene Dialogobjekt delegiert. Wäre ein ActiveDialog-Element vorhanden gewesen, wäre der Aufruf des ContinueDialog-Elements des Dialogs bei der Verarbeitung einfach delegiert worden, sodass dieser Dialog alle zugeordneten gespeicherten Eigenschaften erhalten hätte.

Hinweis: Das **BeginDialog-Element eines Dialogs** ist Initialisierungscode und erfordert Initialisierungseigenschaften (im Code als „options“ Optionen bezeichnet), und das **ContinueDialog-Element eines Dialogs** ist der Code, der ausgeführt wird, um die Ausführung beim Eingang einer Aktivität nach Persistenz fortzusetzen. Beispiel: Angenommen, ein Dialog stellt dem Benutzer eine Frage. Diese Frage wird in „BeginDialog“ gestellt, und die Antwort wird in „ContinueDialog“ erwartet.

Zur Unterstützung der Schachtelung von Dialogen (wenn Dialog einen untergeordneten Dialog aufweist) ist ein zusätzlicher Fortsetzungstyp vorhanden, der als „Wiederaufnahme“ bezeichnet wird. Das DialogContext-Element ruft die ResumeDialog-Methode für einen übergeordneten Dialog auf, wenn ein untergeordneter Dialog abgeschlossen wird.

Eingabeaufforderungen und Wasserfälle sind beides konkrete Beispiele für Dialoge, die vom SDK bereitgestellt werden. Viele Szenarien werden durch Zusammenstellen dieser Abstraktionen erstellt, im Hintergrund beginnt die ausgeführte Logik jedoch immer gleich, d.h. mit dem hier beschriebenen Fortsetzungs- und Wiederaufnahmemuster. Die Implementierung einer Dialogklasse von Grund auf ist ein relativ komplexes Thema, in den [Beispielen](https://github.com/Microsoft/BotBuilder-samples) ist jedoch ein Beispiel dafür enthalten.

Die **Dialogbibliothek** im Bot Builder-SDK enthält integrierte Funktionen wie _Eingabeaufforderungen_, _Wasserfalldialoge_ und _Komponentendialoge_, um Ihnen die Verwaltung der Konversation Ihres Bots zu erleichtern. Sie können Eingabeaufforderungen verwenden, um Benutzer nach verschiedenen Arten von Informationen zu fragen, einen Wasserfall zum Kombinieren mehrerer Schritte in einer Sequenz, und Komponentendialoge zum Verpacken Ihrer Dialoglogik in separate Klassen, die dann in andere Bots integriert werden können.
## <a name="waterfall-dialogs-and-prompts"></a>Wasserfalldialoge und Eingabeaufforderungen

Die **Dialogbibliothek** enthält eine Reihe von Eingabeaufforderungstypen, mit denen Sie verschiedene Arten von Benutzereingaben erfassen können. Beispiel: Um einen Benutzer zu einer Texteingabe aufzufordern, können Sie **TextPrompt** verwenden; um einen Benutzer nach einer Zahl zu fragen, können Sie **NumberPrompt** verwenden; um nach einem Datum und einer Uhrzeit zu fragen, können Sie **DateTimePrompt** verwenden. Eingabeaufforderungen sind ein bestimmter Dialogtyp. Zur Verwendung einer Eingabeaufforderung in einem Wasserfalldialogfeld fügen Sie den Wasserfall und die Eingabeaufforderung demselben Dialogsatz hinzu. 

Aufgrund der Beschaffenheit der Eingabeaufforderung-Antwort-Interaktion erfordert das Implementieren einer Eingabeaufforderung mindestens zwei Schritte in einem Wasserfalldialog – einen zum Senden der Eingabeaufforderung und einen zweiten zum Erfassen und Verarbeiten der Antwort.  Wenn Sie eine zusätzliche Eingabeaufforderung haben, können Sie diese manchmal kombinieren, indem Sie mit einer einzelnen Funktion zuerst die Antwort des Benutzers verarbeiten und dann die nächste Eingabeaufforderung starten.

Ein `WaterfallDialog` ist eine spezifische Implementierung eines Dialogs, der dazu verwendet wird, Informationen vom Benutzer zu sammeln oder den Benutzer durch eine Reihe von Aufgaben zu leiten. Die Aufgaben werden als Array von Funktionen implementiert, bei dem das Ergebnis der ersten Funktion als Argument an die nächste Funktion übergeben wird usw. Jede Funktion stellt normalerweise einen Schritt im gesamten Prozess dar. Bei jedem Schritt fordert der Bot den Benutzer zur Eingabe auf, wartet auf eine Antwort und übergibt die Ergebnisse dann an den nächsten Schritt. 

Eingabeaufforderungen und Wasserfall sind beides Dialoge, wie in der folgenden Klassenhierarchie dargestellt. 

![Dialogklassen](media/bot-builder-dialog-classes.png)

Ein Wasserfalldialog besteht aus einer Sequenz von Wasserfallschritten. Jeder Schritt ist ein asynchroner Delegat, der einen _Wasserfall-Schrittkontextparameter_ (`step`) verwendet. Das Muster sieht vor, dass als letzte Aktion in einem Wasserfallschritt entweder ein untergeordneter Dialog (in der Regel eine Eingabeaufforderung) begonnen oder der Wasserfall selbst beendet wird. Das folgende Diagramm zeigt eine Sequenz von Wasserfallschritten und die ausgeführten Stapelvorgänge.

![Dialogkonzept](media/bot-builder-dialog-concept.png)

Sie können einen Rückgabewert aus einem Dialog entweder innerhalb eines Wasserfallschritts oder im on-turn-Handler Ihres Bots verarbeiten.
In einem Wasserfallschritt stellt der Dialog den Rückgabewert in der _result_-Eigenschaft des Wasserfall-Schrittkontexts bereit.
In der Regel müssen Sie nur den Status des Dialogdurchlauf-Ergebnisses aus der Durchlauflogik Ihres Bots überprüfen.

## <a name="repeating-a-dialog"></a>Wiederholen eines Dialogs

Verwenden Sie zum Wiederholen eines Dialogs die *replace dialog*-Methode. Die *replace dialog*-Methode des Dialogkontexts entfernt den aktuellen Dialog aus dem Stapel, verschiebt den ersetzenden Dialog an den Anfang des Stapels und startet diesen Dialog. Mit dieser Methode können Sie also Schleife erstellen, indem Sie einen Dialog durch sich selbst ersetzen. Hinweis: Wenn der interne Zustand für den aktuellen Dialog beibehalten werden muss, müssen Sie im Aufruf der _replace dialog_-Methode Informationen an die neue Instanz des Dialogs übergeben und dann den Dialog entsprechend initialisieren. Der Zugriff auf die in den neuen Dialog übergebenen Optionen ist in jedem Schritt des Dialogs über die _options_-Eigenschaft des Schrittkontexts möglich. Dies ist eine hervorragende Möglichkeit zum Behandeln eines komplexen Konversationsablaufs oder zum Verwalten von Menüs.

## <a name="branch-a-conversation"></a>Verzweigen einer Konversation

Der Dialogkontext pflegt einen _Dialogstapel_ und verfolgt für jeden Dialog aus dem Stapel nach, welcher Schritt als Nächstes ansteht. Die Methode _begin dialog_ fügt per Push ganz oben im Stapel einen Dialog ein, und die Methode _end dialog_ entfernt den obersten Dialog per Pop aus dem Stapel.

Ein Dialog kann innerhalb des gleichen Dialogsatzes einen neuen Dialog starten. Dazu wird die Methode _begin dialog_ des Dialogkontexts aufgerufen und die ID des neuen Dialogs angegeben. Dadurch wird der neue Dialog zum aktuell aktiven Dialog. Der ursprüngliche Dialog befindet sich zwar weiterhin im Stapel, Aufrufe der Methode _continue dialog_ des Dialogkontexts werden aber nur an den Dialog gesendet, der sich ganz oben im Stapel befindet (_aktiver Dialog_). Wenn ein Dialog per Pop aus dem Stapel entfernt wird, fährt der Dialogkontext mit dem Schritt des Wasserfalls aus dem Stapel fort, der im ursprünglichen Dialog als nächstes an der Reihe wäre.

Somit können Sie eine Verzweigung innerhalb Ihres Konversationsablaufs erstellen, indem Sie einen Schritt in einen Dialog einschließen, der bei Bedarf einen Dialog aus einer Gruppe verfügbarer Dialoge startet.

## <a name="component-dialog"></a>Komponentendialog
Manchmal bietet es sich an, einen wiederverwendbaren Dialog zu schreiben, den Sie in verschiedenen Szenarien verwenden möchten. Ein Beispiel dafür wäre ein Adressendialog, bei dem der Benutzer zur Angabe von Werten für Straße, Ort und Postleitzahl aufgefordert werden. 

Das ComponentDialog-Element bietet ein gewisses Maß an Isolation, da es über ein separates DialogSet-Element verfügt. Durch ein separates DialogSet-Element werden Namenskonflikte mit dem übergeordneten Element verhindert, das den Dialog enthält. Das DialogSet-Element erstellt eine eigene unabhängige interne Dialoglaufzeit (durch Erstellung eines eigenen DialogContext-Element) und sendet die Aktivität an dieses. Dieser sekundäre Sendevorgang bedeutet, dass das Element eine Möglichkeit zum Abfangen der Aktivität hatte. Dies kann sehr nützlich, wenn Sie Funktionen wie „Hilfe“ und „Abbrechen“ implementieren möchten.  Sehen Sie sich das Vorlagenbeispiel für Bots für Unternehmen an. 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erfassen von Benutzereingaben mit der Dialogebibliothek](bot-builder-prompts.md)

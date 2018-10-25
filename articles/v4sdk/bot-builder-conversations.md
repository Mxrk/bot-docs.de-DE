---
title: Konversationen innerhalb des Bot Builder SDK | Microsoft-Dokumentation
description: Beschreibt, wie eine Konversation innerhalb des Bot Builder SDK abläuft.
keywords: Konversationsfluss, Erkennen der Absicht, Einzeldurchlauf, Mehrfachdurchlauf, Botkonversation
author: jonathanfingold
ms.author: jonathanfingold
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 09/01/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 55145b4728d325bd258cc2cd95f3a265aaa50b0a
ms.sourcegitcommit: b8bd66fa955217cc00b6650f5d591b2b73c3254b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49326457"
---
# <a name="conversation-flow"></a>Konversationsablauf
[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Bei der Gestaltung des Konversationsablaufs eines Bots muss entschieden werden, wie ein Bot reagiert, wenn der Benutzer etwas zu ihm sagt. Ein Bot erkennt zunächst die Aufgabe oder das Gesprächsthema anhand einer Nachricht des Benutzers. Um die Aufgabe oder das Thema (als *Absicht* bezeichnet) zu bestimmen, kann der Bot nach Wörtern oder Mustern im Nachrichtentext des Benutzers suchen, oder er kann Dienste wie [Language Understanding](bot-builder-concept-luis.md) und [QnA Maker](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/overview/overview) nutzen.

Sobald der Bot die Absicht des Benutzers erkannt hat, kann er, je nach Szenario, die Anforderung des Benutzers mit einer einzigen Antwort erfüllen und das Gespräch in einem Durchlauf abschließen, oder es sind ggf. mehrere Durchläufe erforderlich. Für Konversationsabläufe mit mehreren Durchläufen ermöglicht das Bot Builder SDK die [Zustandsverwaltung](./bot-builder-howto-v4-state.md) zur Verfolgung einer Konversation, [Eingabeaufforderungen](bot-builder-prompts.md) zur Anforderung von Informationen und [Dialoge](bot-builder-dialog-manage-conversation-flow.md) zur Kapselung von Konversationsflüssen.

In einem komplexen Bot mit mehreren Subsystemen kann es vorkommen, dass Sie mehrere Dienste zum Erkennen der Absicht verwenden – jeweils einen für jede Unterkomponente des Bot. Das [Dispatch-Tool](bot-builder-tutorial-dispatch.md) liefert die Ergebnisse mehrerer Dienste zentral an einem Ort, wenn Sie Konversationssubsysteme zu einem Bot zusammenfassen.

<!-- 
A conversation identifies a series of activities sent between a bot and a user on a specific channel and represents an interaction between one or more bots and either a _direct_ conversation with a specific user or a _group_ conversation with multiple users.
A bot communicates with a user on a channel by receiving activities from, and sending activities to the user.

- Each user has an ID that is unique per channel.
- Each conversation has an ID that is unique per channel.
- The channel sets the conversation ID when it starts the conversation.
- The bot cannot start a conversation; however, once it has a conversation ID, it can resume that conversation.
- Not all channels support group conversations.
-->

## <a name="single-turn-conversation"></a>Konversation mit Einzeldurchlauf

Der einfachste Konversationsablauf ist ein Einzeldurchlauf. In einem Konversationsablauf mit Einzeldurchlauf beendet der Bot seine Aufgabe in einem Durchlauf. Dieser besteht aus einer Nachricht vom Benutzer und einer Antwort vom Bot.

<!-- The following isn't always true, it's a generalization -->

Bei der einfachsten Art von Bots mit Einzeldurchlauf müssen Sie den Zustand der Konversation nicht nachverfolgen. Jedes Mal, wenn der Bot eine Nachricht erhält, reagiert er nur auf der Grundlage des Kontexts der aktuellen eingehenden Nachricht, ohne Kenntnis vergangener Konversationsdurchläufe.

![Wetterbot mit Einzeldurchlauf](./media/concept-conversation/weather-single-turn.png)

Für einen Wetterbot wird unter Umständen nur ein Durchlauf verwendet, und der Benutzer erhält nur einen Wetterbericht, ohne dass nach der Stadt oder dem Datum gefragt wird. Die gesamte Logik für die Anzeige des Wetterberichts basiert auf der Nachricht, die der Bot gerade erhalten hat. In jedem Durchlauf bzw. Turn einer Konversation erhält der Bot einen [Turn-Kontext](bot-builder-concept-activity-processing.md#turn-context), mit dem er bestimmen kann, was als Nächstes zu tun ist und wie die Konversation abläuft.

## <a name="multiple-turns"></a>Mehrfachdurchläufe

Die meisten Konversationstypen können nicht in einem einzigen Durchlauf abgeschlossen werden, sodass ein Bot auch einen Konversationsablauf mit mehreren Durchläufen haben kann. Einige Szenarien, die mehrere Konversationsdurchläufe erfordern, sind:

* Ein Bot, der den Benutzer nach zusätzlichen Informationen fragt, die er benötigt, um eine Aufgabe zu erledigen. Der Bot muss verfolgen, ob er alle Parameter für die Erfüllung der Aufgabe hat.
* Ein Bot, der den Benutzer durch Schritte in einem Prozess führt, wie z.B. das Aufgeben einer Bestellung. Der Bot muss verfolgen, an welcher Stelle sich der Benutzer in der Abfolge der Schritte befindet.

Zum Beispiel kann ein Wetterbot einen Konversationsfluss mit mehreren Durchläufen nutzen, wenn der Bot auf die Frage „What‘s the weather?“ (Wie ist das Wetter?) nach der Stadt fragt.

![Wetterbot mit mehreren Durchläufen](./media/concept-conversation/weather-multi-turn.png)

Wenn der Benutzer auf die Nachfrage des Bots nach einer Stadt antwortet und der Bot die Antwort „Seattle“ erhält, muss im Bot ein Kontext gespeichert sein, sodass er versteht, dass die aktuelle Nachricht die Antwort auf eine frühere Aufforderung und Teil einer Wetteranfrage ist. Bots mit mehreren Durchläufen behalten den Zustand im Auge, um auf neue Nachrichten angemessen zu reagieren.

Weitere Informationen finden Sie unter [Verwalten des Konversations- und Benutzerzustands](bot-builder-howto-v4-state.md).

> [!NOTE]
> Konversationen mit mehreren Durchläufen mit REST-API-Clients müssen ihren eigenen Zustand verfolgen, beispielsweise in einer Datenbank oder einem Tabellenspeicher.

## <a name="conversation-topics"></a>Konversationsthemen

Sie können Ihren Bot so gestalten, dass er mehr als eine Art von Aufgabe übernimmt. Zum Beispiel können Sie einen Bot mit verschiedenen Konversationsflüssen für das Begrüßen des Benutzers, das Aufgeben oder Stornieren einer Bestellung und das Anfordern von Hilfe einrichten. Eine Möglichkeit, diesen Wechsel zwischen Konversationen für verschiedene Aufgaben oder Gesprächsthemen zu verarbeiten, besteht darin, in der aktuellen Nachricht die Absicht (was der Benutzer tun möchte) zu erkennen.

### <a name="recognize-intent"></a>Erkennen der Absicht

Das Bot Builder SDK enthält _Erkennungen_, mit denen eine Nachricht verarbeitet werden kann, um die Absicht zu bestimmen, damit Ihr Bot den entsprechenden Konversationsfluss initiieren kann. Rufen Sie die asynchrone _recognize_-Methode der Erkennung auf, um die Absicht des Benutzers aus dem Nachrichteninhalt zu ermitteln. Anschließend können Sie die _get top scoring intent_-Methode für das Ergebnis aufrufen, um die beste Vorhersage der Erkennung zu erhalten.

Für eine Erkennung können reguläre Ausdrücke, Language Understanding oder eine andere von Ihnen entwickelte Logik verwendet werden. Hier sind Beispiele für mögliche Erkennungen angegeben:

* Eine Erkennung, für die mit QnA Maker erkannt wird, wann ein Benutzer eine Frage stellt, für die in der Wissensdatenbank eine Antwort enthalten ist.
* Eine Erkennung mit Language Understanding (LUIS), um einen Dienst mit Beispielen zu trainieren, wie Benutzer um Hilfe bitten können. Dies wird dann der Absicht `Help` zugeordnet.
* Eine benutzerdefinierte Erkennung, für die reguläre Ausdrücke verwendet werden, um nach Befehlen zu suchen.
* Eine benutzerdefinierte Erkennung, für die ein Dienst zum Übersetzen der Eingabe verwendet wird.

### <a name="consider-how-to-interrupt-conversation-flow-or-change-topics"></a>Unterbrechen des Konversationsablaufs oder Ändern von Themen

Eine Möglichkeit, den Überblick zu behalten, an welcher Stellen Sie sich in der Konversation befinden, besteht darin, den [Konversationszustand](bot-builder-howto-v4-state.md) zu verwenden, um Informationen über das gerade aktive Thema zu speichern oder darüber, welche Schritte in einer Abfolge abgeschlossen wurden.

Wenn ein Bot komplexer wird, können Sie auch eine Abfolge von Konversationsabläufen in einem Stapel erstellen, z.B. wird der Bot den neuen Auftragsablauf aufrufen und dann den Ablauf für die Produktsuche. Der Benutzer wählt anschließend ein Produkt aus und bestätigt, sodass die Produktsuche und danach die Bestellung abgeschlossen wird.

Konversationen folgen aber selten einem solch linearen, logischen Pfad. Benutzer kommunizieren nicht in „Stapeln“, sondern tendieren stattdessen dazu, häufig ihre Meinung zu ändern. Betrachten Sie das folgende Beispiel:

![Der Benutzer sagt etwas unerwartetes](./media/concept-conversation/interruption.png)

Obwohl Ihr Bot einen logisch konstruierten Stapel von Abläufen erstellt hat, entscheidet der Benutzer, etwas ganz anderes zu tun oder eine Frage zu stellen, die mit dem aktuellen Thema nichts zu tun hat. Im Beispiel stellt der Benutzer eine Frage anstatt die Ja-Nein-Antwort zu geben, die laut Ablauf zu erwarten ist. Wie soll Ihr Bot reagieren?

* Er könnte darauf bestehen, dass der Benutzer zunächst die Frage beantwortet.
* Er könnte alle zuvor vom Benutzer getroffenen Entscheidungen verwerfen, den kompletten Ablaufstapel zurücksetzen und ganz neu beginnen, indem er versucht, die Frage des Benutzers zu beantworten.
* Er könnte versuchen, die Frage des Benutzers zu beantworten und dann zu dieser Ja-Nein-Frage zurückkehren und versuchen, erneut von dort fortzufahren.

Es gibt keine richtige Antwort auf diese Frage. Die beste Lösung hängt von der jeweiligen Situation ab und davon, was Benutzer als angemessene Reaktion des Bots erwarten würden. Lesen Sie im Hinblick auf die Verwaltung des Konversationsflusses die Artikel [Verwalten eines einfachen Konversationsflusses mit Dialogen](bot-builder-dialog-manage-conversation-flow.md) und [Behandeln von Benutzerunterbrechungen](bot-builder-howto-handle-user-interrupt.md).

## <a name="conversation-lifetime"></a>Lebensdauer der Konversation

<!-- Note: these activities are dependent on whether the channel actually sends them. Also, we should add links -->Ein Bot empfängt eine _conversation update_-Aktivität, wenn er einer Konversation hinzugefügt wurde, andere Mitglieder hinzugefügt bzw. aus einer Konversation entfernt wurden oder Konversationsmetadaten geändert wurden.
Möglicherweise möchten Sie, dass Ihr Bot auf Konversationsupdates reagiert, indem er Benutzer begrüßt oder sich vorstellt.

Ein Bot empfängt eine _end of conversation_-Aktivität, um anzugeben, dass der Benutzer die Konversation beendet hat. Ein Bot kann eine _end of conversation_-Aktivität senden, um anzugeben, dass die Konversation beendet wird.
Wenn Sie Informationen über die Konversation speichern, können Sie diese Informationen nach Ende der Konversation löschen.

<!--  Types of conversations -->

Ihr Bot kann Interaktionen mit mehreren Durchläufen unterstützen, bei denen unterschiedliche Informationen von Benutzern abgefragt werden. Hierbei kann es um eine spezifische Aufgabe oder mehrere Arten von Aufgaben gehen.
Das Bot Builder SDK verfügt über integrierte Unterstützung für Language Understanding (LUIS) und QnA Maker, damit Sie Ihrem Bot Features vom Typ „Frage und Antwort“ in natürlicher Sprache hinzufügen können.

## <a name="conversations-channels-and-users"></a>Konversationen, Kanäle und Benutzer

Konversationen können entweder _direkte_ Konversationen mit einem bestimmten Benutzer oder _Gruppenkonversationen_mit mehreren Benutzern sein.
Ein Bot kommuniziert mit einem Benutzer auf einem Kanal, indem er Aktivitäten von ihm empfängt und an ihn sendet.

* Jeder Benutzer hat eine eindeutige ID pro Kanal.
* Jede Konversation hat eine eindeutige ID pro Kanal.
* Die ID der Konversation wird zu Beginn der Konversation vom Kanal festgelegt.
* Der Bot kann keine Konversation starten; sobald er jedoch eine Konversations-ID erhält, kann er die Konversation wieder aufnehmen.
* Nicht alle Kanäle unterstützen Gruppenkonversationen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwalten eines einfachen Konversationsflusses mit Dialogen](bot-builder-dialog-manage-conversation-flow.md)

<!-- In addition, your bot can send activities back to the user, either _proactively_, in response to internal logic, or _reactively_, in response to an activity from the user or channel.-->
<!--TODO: Link to messaging how tos.-->

---
title: Konversationen innerhalb des Bot Builder SDK | Microsoft-Dokumentation
description: Beschreibt, wie eine Konversation innerhalb des Bot Builder SDK abläuft.
keywords: konversationsablauf, erkennen der absicht, einzeldurchlauf, mehrfachdurchlauf
author: jonathanfingold
ms.author: jonathanfingold
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 04/11/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 32486cb024dfe852a7478ccba4a0eedc476431b0
ms.sourcegitcommit: ee63d9dc1944a6843368bdabf5878950229f61d0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2018
ms.locfileid: "42795029"
---
# <a name="conversation-flow"></a>Konversationsablauf
[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Stellen Sie sich einen Bot als dialogorientierte Benutzeroberfläche vor. Der Gesprächsablauf ist daher die Art und Weise, wie wir mit dem Benutzer interagieren, und diese Interaktion kann verschiedene Formen annehmen. Der richtige Gesprächsablauf ermöglicht eine besser Interaktion mit dem Benutzer und eine höhere Leistung Ihres Bots.

Bei der Gestaltung des Konversationsablaufs eines Bots muss entschieden werden, wie ein Bot reagiert, wenn der Benutzer etwas zu ihm sagt. Ein Bot erkennt zunächst die Aufgabe oder das Gesprächsthema anhand einer Nachricht des Benutzers. Um die Aufgabe oder das Thema (als *Absicht* bezeichnet) zu bestimmen, kann der Bot nach Wörtern oder Mustern im Nachrichtentext des Benutzers suchen, oder er kann Dienste wie [Language Understanding (LUIS)](bot-builder-concept-luis.md) und QnA Maker nutzen. 

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



<!-- 
The EchoBot sample in the BotBuilder SDK is a single-turn bot. Here are other examples of single turn conversation flow:
* A bot for getting the weather report, that just tells the user what the weather is, when they say "What's the weather?".
* An IoT bot that responds to "turn on the lights" by calling an IoT service. -->

<!-- The following isn't always true, it's a generalization --> Bei der einfachsten Art von Bots mit Einzeldurchlauf müssen Sie nicht den Zustand der Konversation nachverfolgen. Jedes Mal, wenn der Bot eine Nachricht erhält, reagiert er nur auf der Grundlage des Kontexts der aktuellen eingehenden Nachricht, ohne Kenntnis vergangener Konversationsdurchläufe.

![Wetterbot mit Einzeldurchlauf](./media/concept-conversation/weather-single-turn.png)

Ein Wetterbot arbeitet nur mit einem Durchlauf und gibt dem Benutzer nur einen Wetterbericht, ohne erneut nach der Stadt oder dem Datum zu fragen. Die gesamte Logik für die Anzeige des Wetterberichts basiert auf der Nachricht, die der Bot gerade erhalten hat. In jedem Durchlauf bzw. Turn einer Konversation erhält der Bot einen [Turn-Kontext](bot-builder-concept-activity-processing.md#turn-context), mit dem er bestimmen kann, was als Nächstes zu tun ist und wie die Konversation abläuft. 

## <a name="multiple-turns"></a>Mehrfachdurchläufe

Die meisten Konversationstypen können nicht in einem einzigen Durchlauf abgeschlossen werden, sodass ein Bot auch einen Konversationsablauf mit mehreren Durchläufen haben kann. Einige Szenarien, die mehrere Konversationsdurchläufe erfordern, sind:

 * Ein Bot, der den Benutzer nach zusätzlichen Informationen fragt, die er benötigt, um eine Aufgabe zu erledigen. Der Bot muss verfolgen, ob er alle Parameter für die Erfüllung der Aufgabe hat.
 * Ein Bot, der den Benutzer durch Schritte in einem Prozess führt, wie z.B. das Aufgeben einer Bestellung. Der Bot muss verfolgen, an welcher Stelle sich der Benutzer in der Abfolge der Schritte befindet.

Zum Beispiel hat ein Wetterbot einen Konversationsablauf mit mehreren Durchläufen, wenn der Bot auf die Frage „What‘s the weather?“ (Wie ist das Wetter?) nach der Stadt fragt.

![Wetterbot mit mehreren Durchläufen](./media/concept-conversation/weather-multi-turn.png)

Wenn der Benutzer auf die Nachfrage des Bots nach einer Stadt antwortet und der Bot die Antwort „Seattle“ erhält, muss im Bot ein Kontext gespeichert sein, sodass er versteht, dass die aktuelle Nachricht die Antwort auf eine frühere Aufforderung und Teil einer Wetteranfrage ist. Bots mit mehreren Durchläufen behalten den Zustand im Auge, um auf neue Nachrichten angemessen zu reagieren.

<!--
```
// TBD: snippet showing receiving message and using ConversationProperties
```
-->

Einen Überblick über den Verwaltungszustand finden Sie unter [Verwalten des Zustands](bot-builder-storage-concept.md) und ein Beispiel unter [Verwenden von Benutzer- und Konversationseigenschaften](bot-builder-howto-v4-state.md).

> [!NOTE]
> Konversationen mit mehreren Durchläufen mit REST-API-Clients müssen ihren eigenen Zustand verfolgen, beispielsweise in einer Datenbank oder einem Tabellenspeicher. 

## <a name="conversation-topics"></a>Konversationsthemen

Sie können Ihren Bot so gestalten, dass er mehr als eine Art von Aufgabe übernimmt. Zum Beispiel können Sie einen Bot mit verschiedenen Konversationsflüssen für die Begrüßung des Benutzers, das Aufgeben einer Bestellung, das Abbrechen und das Abrufen von Hilfe einrichten. Eine Möglichkeit, diesen Wechsel zwischen Konversationen für verschiedene Aufgaben oder Gesprächsthemen zu verarbeiten, besteht darin, in der aktuellen Nachricht die Absicht (was der Benutzer tun möchte) zu erkennen. 

### <a name="recognize-intent"></a>Erkennen der Absicht

Das Bot Builder SDK beinhaltet _Erkennungen_, die jede eingehende Nachricht verarbeiten, um die Absicht zu bestimmen, sodass Ihr Bot den entsprechenden Konversationsablauf initiieren kann. Vor dem _Empfangsrückruf_ analysieren die Erkennungen den Nachrichteninhalt des Benutzers, um die Absicht zu bestimmen, und geben diese an den Bot zurück, indem sie das Turn-Kontextobjekt innerhalb des Empfangsrückrufs verwenden, das als **am höchsten eingestufte Absicht** im [Turn-Kontextobjekt](bot-builder-concept-activity-processing.md#turn-context) gespeichert ist. 

Die Erkennung, die die **Top Intent** bestimmt, kann einfach reguläre Ausdrücke, Language Understanding (LUIS) oder eine andere Logik verwenden, die Sie als Middleware entwickeln. Im Folgenden finden Sie einige Beispiele für Erkennungen:
   
* Sie richten eine Erkennung ein, die reguläre Ausdrücke verwendet, um jedes Mal zu erkennen, wenn ein Benutzer das Wort „Help“ (Hilfe) sagt.
* Sie verwenden Language Understanding (LUIS), um einen Dienst mit Beispielen zu trainieren, wie Benutzer um Hilfe bitten können, und ordnen dies der Absicht „Help“ (Hilfe) zu.
* Sie erstellen eine eigene Erkennungsmiddleware, die eingehende Aktivitäten prüft und jedes Mal, wenn sie eine Nachricht in einer anderen Sprache erkennt, die Absicht „Translate“ (Übersetzen) zurückgibt.

Weitere Informationen finden Sie unter [Language Understanding mit LUIS](bot-builder-concept-luis.md). <!-- TODO: ADD THIS TOPIC OR SNIPPET-->

### <a name="consider-how-to-interrupt-conversation-flow-or-change-topics"></a>Unterbrechen des Konversationsablaufs oder Ändern von Themen

Eine Möglichkeit, den Überblick zu behalten, an welcher Stellen Sie sich in der Konversation befinden, besteht darin, den [Konversationszustand](bot-builder-howto-v4-state.md) zu verwenden, um Informationen über das gerade aktive Thema zu speichern oder darüber, welche Schritte in einer Abfolge abgeschlossen wurden.

Wenn ein Bot komplexer wird, können Sie auch eine Abfolge von Konversationsabläufen in einem Stapel erstellen, z.B. wird der Bot den neuen Auftragsablauf aufrufen und dann den Ablauf für die Produktsuche. Der Benutzer wählt anschließend ein Produkt aus und bestätigt, sodass die Produktsuche und danach die Bestellung abgeschlossen wird.

Konversation folgen jedoch selten einem solch linearen, logischen Pfad. Benutzer kommunizieren nicht in „Stapeln“, sondern tendieren stattdessen dazu, häufig ihre Meinung zu ändern. Betrachten Sie das folgende Beispiel:

![Der Benutzer sagt etwas unerwartetes](./media/concept-conversation/interruption.png)

Obwohl Ihr Bot einen logisch konstruierten Stapel von Abläufen erstellt hat, entscheidet der Benutzer, etwas ganz anderes zu tun oder eine Frage zu stellen, die mit dem aktuellen Thema nichts zu tun hat. Im Beispiel stellt der Benutzer eine Frage anstatt die Ja-Nein-Antwort zu geben, die laut Ablauf zu erwarten ist. Wie soll Ihr Bot reagieren?

* Er könnte darauf bestehen, dass der Benutzer zunächst die Frage beantwortet.
* Er könnte alle zuvor vom Benutzer getroffenen Entscheidungen verwerfen, den kompletten Ablaufstapel zurücksetzen und ganz neu beginnen, indem er versucht, die Frage des Benutzers zu beantworten.
* Er könnte versuchen, die Frage des Benutzers zu beantworten und dann zu dieser Ja-Nein-Frage zurückkehren und versuchen, erneut von dort fortzufahren.

Es gibt keine richtige Antwort auf diese Frage. Die beste Lösung hängt von der jeweiligen Situation ab und davon, was Benutzer als angemessene Reaktion des Bots erwarten würden. Weitere Informationen finden Sie unter [Behandeln von Benutzerunterbrechungen](bot-builder-howto-handle-user-interrupt.md).

> [!TIP]
> Mit dem Bot Builder SDK für Node.js können Sie [Dialoge](bot-builder-dialog-manage-conversation-flow.md) zum Verwalten des Konversationsablaufs verwenden.

## <a name="conversation-lifetime"></a>Lebensdauer der Konversation

<!-- Note: these activities are dependent on whether the channel actually sends them. Also, we should add links -->Ein Bot empfängt eine _conversation update_-Aktivität, wenn er einer Konversation hinzugefügt wurde, andere Mitglieder hinzugefügt bzw. aus einer Konversation entfernt wurden oder Konversationsmetadaten geändert wurden.
Möglicherweise möchten Sie, dass Ihr Bot auf Konversationsupdates reagiert, indem er Benutzer begrüßt oder sich vorstellt.

Ein Bot empfängt eine _end of conversation_-Aktivität, um anzugeben, dass der Benutzer die Konversation beendet hat. Ein Bot kann eine _end of conversation_-Aktivität senden, um dem Benutzer mitzuteilen, dass die Konversation beendet wird. Wenn Sie Informationen über die Konversation speichern, können Sie diese Informationen nach Ende der Konversation löschen.

<!--  Types of conversations

Your bot can support multi-turn interactions where it prompts users for multiple peices of information. It can be focused on a very specific task or support multiple types of tasks. 
The Bot Builder SDK has some built-in support for Language Understatnding (LUIS) and QnA Maker for adding natural language "question and answer" features to your bot.

<!--TODO: Add with links when these topics are available:
[Conversation flow] and other design articles.
[Using recognizers] [Using state and storage] and other how tos.
-->
## <a name="conversations-channels-and-users"></a>Konversationen, Kanäle und Benutzer

Konversationen können entweder _direkte_ Konversationen mit einem bestimmten Benutzer oder _Gruppenkonversationen_mit mehreren Benutzern sein.
Ein Bot kommuniziert mit einem Benutzer auf einem Kanal, indem er Aktivitäten von ihm empfängt und an ihn sendet.

- Jeder Benutzer hat eine eindeutige ID pro Kanal.
- Jede Konversation hat eine eindeutige ID pro Kanal.
- Die ID der Konversation wird zu Beginn der Konversation vom Kanal festgelegt.
- Der Bot kann keine Konversation starten; sobald er jedoch eine Konversations-ID erhält, kann er die Konversation wieder aufnehmen.
- Nicht alle Kanäle unterstützen Gruppenkonversationen.

## <a name="next-steps"></a>Nächste Schritte

Für komplexe Konversationen, wie einige der oben genannten, muss es möglich sein, Informationen länger als nur für einen Durchlauf zu speichern. Schauen wir uns jetzt das Thema „Zustand und Speicher“ an.

> [!div class="nextstepaction"]
> [Zustand und Speicher](bot-builder-storage-concept.md)

<!-- In addition, your bot can send activities back to the user, either _proactively_, in response to internal logic, or _reactively_, in response to an activity from the user or channel.-->
<!--TODO: Link to messaging how tos.-->

<!--  TODO: Change to next steps, one for each of LUIS and State
## See also

- Activities
- Adapter
- Context
- Proactive messaging
- State
-->

[QnAMaker]:(bot-builder-luis-and-qna.md#using-qna-maker)

<!-- TODO: Update when the Dispatch concept is pushed -->
[Dispatch]:(bot-builder-concept-luis.md)

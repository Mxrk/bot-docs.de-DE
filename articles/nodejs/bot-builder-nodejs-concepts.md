---
title: Wichtige Begriffe im Bot Builder SDK für Node.js | Microsoft-Dokumentation
description: Lernen Sie wichtige Begriffe und Tools zum Erstellen und Bereitstellen von Unterhaltungsbots kennen, die im Bot Builder SDK für Node.js verfügbar sind.
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: fd81b9dfe5d3e16096ffd6ab25c1ee23ff77f79f
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42904928"
---
# <a name="key-concepts-in-the-bot-builder-sdk-for-nodejs"></a>Wichtige Begriffe im Bot Builder SDK für Node.js

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-concepts.md)
> - [Node.js](../nodejs/bot-builder-nodejs-concepts.md)

Dieser Artikel stellt wichtige Begriffe im Bot Builder SDK für Node.js vor. Eine Einführung in Bot Framework finden Sie in der [Bot Framework-Übersicht](../overview-introduction-bot-framework.md).

## <a name="connector"></a>Connector

Der Bot Framework Connector ist ein Dienst, der Ihren Bot mit mehreren *Kanälen* verbindet, die Clients sind, z.B. Skype, Slack, Facebook und SMS. Er ermöglicht die Kommunikation zwischen Bot und Benutzer, indem Nachrichten vom Bot zum Kanal und vom Kanal zum Bot weitergeleitet werden. Die Logik Ihres Bots wird als Webdienst gehostet, der Nachrichten von Benutzern über den Connector-Dienst empfängt. Die Antworten Ihres Bots werden über HTTPS POST an den Connector gesendet. 

Das Bot Builder SDK für Node.js stellt die Klassen [UniversalBot][UniversalBot] und [ChatConnector][ChatConnector] für die Konfiguration des Bot zum Senden und Empfangen von Nachrichten über den Bot Framework Connector bereit. Die Klasse `UniversalBot` bildet sozusagen das Gehirn Ihres Bots. Sie ist verantwortlich für die Verwaltung aller Unterhaltungen, die Ihr Bot mit einem Benutzer führt. Die `ChatConnector`-Klasse verbindet Ihren Bot mit dem Dienst Bot Framework Connector.
Ein Beispiel für die Verwendung dieser Klassen finden Sie unter [Erstellen eines Bots mit dem Bot Builder SDK für Node.js](bot-builder-nodejs-quickstart.md).

Der Connector normalisiert auch die Nachrichten, die der Bot an die Kanäle sendet, sodass Sie Ihren Bot plattformunabhängig entwickeln können. Das Normalisieren einer Nachricht schließt das Konvertieren vom Bot Framework-Schema zum Kanalschema ein. Wenn der Kanal nicht alle Aspekte des Bot Framework-Schemas unterstützt, versucht der Connector, die Nachricht in ein Format zu konvertieren, das der Kanal unterstützt. Wenn der Bot beispielsweise eine Nachricht mit einer Karte mit interaktiven Schaltflächen an den SMS-Kanal sendet, kann der Connector die Karte als Bild rendern und die Aktionen als Links in den Nachrichtentext aufnehmen. Das Webtool [Channel Inspector][ChannelInspector] zeigt, wie der Connector die Nachrichten auf verschiedenen Kanälen rendert.

Für `ChatConnector` muss ein API-Endpunkt in Ihrem Bot eingerichtet sein. Mit dem Node.js SDK erfolgt dies in der Regel durch die Installation des Node.js-Moduls `restify`. Bots können auch für die Konsole mit [ConsoleConnector][ConsoleConnector] erstellt werden. In diesem Fall wird kein API-Endpunkt benötigt.

## <a name="messages"></a>Meldungen

Nachrichten können aus anzuzeigendem Text, zu sprechendem Text, Anlagen, Rich Cards und vorgeschlagenen Aktionen bestehen. Mit der Methode [session.send][SessionSend] können Sie Nachrichten als Antwort auf eine Benutzernachricht senden. Ihr Bot kann `send` beliebig oft als Antwort auf eine Nachricht des Benutzers aufrufen. Ein veranschaulichendes Beispiel finden Sie unter [Antworten auf Benutzernachrichten][RespondMessages].

Ein Beispiel für das Senden einer Rich Card mit interaktiven Schaltflächen, die Benutzer anklicken, um Aktionen zu starten, finden Sie im Artikel [Hinzufügen von Rich Cards zu Nachrichten](bot-builder-nodejs-send-rich-cards.md). Der Artikel [Senden von Anlagen](bot-builder-nodejs-send-receive-attachments.md) enthält ein Beispiel für das Senden und Empfangen von Anlagen. Wenn Sie ein Beispiel für das Senden einer Nachricht suchen, die Text angibt, der vom Bot auf einem sprachaktivierten Kanal gesprochen werden soll, lesen Sie [Hinzufügen von Sprache zu Nachrichten](bot-builder-nodejs-text-to-speech.md). Ein Beispiel für das Senden vorgeschlagener Aktionen finden Sie unter [Senden vorgeschlagener Aktionen](bot-builder-nodejs-send-suggested-actions.md).

## <a name="dialogs"></a>Dialoge
Dialoge helfen dabei, die Unterhaltungslogik in Ihrem Bot zu organisieren, und sind grundlegend für [die Gestaltung des Unterhaltungsflusses](../bot-service-design-conversation-flow.md). Eine Einführung in Dialoge erhalten Sie unter [Verwalten einer Unterhaltung mit Dialogen](bot-builder-nodejs-dialog-manage-conversation.md).

## <a name="actions"></a>Aktionen
Es ist ratsam, den Bot so zu entwickeln, dass er Unterbrechungen wie Abbruch- oder Hilfeanforderungen jederzeit in einer Unterhaltung verarbeiten kann. Das Bot Builder SDK für Node.js bietet globale Meldungshandler, die Aktionen wie das Abbrechen oder Aufrufen anderer Dialoge auslösen. Unter [Behandeln von Benutzeraktionen](bot-builder-nodejs-dialog-actions.md) finden Sie Beispiele zum Verwenden von [triggerAction][triggerAction]-Handlern.
<!--[Handling cancel](bot-builder-nodejs-manage-conversation-flow.md#handling-cancel), [Confirming interruptions](bot-builder-nodejs-manage-conversation-flow.md#confirming-interruptions) and-->


## <a name="recognizers"></a>Erkennungen
Wenn Bots Benutzeranforderungen erhalten, z.B. „Hilfe“ oder „Nachrichten finden“, müssen sie verstehen, was der Benutzer möchte, und dann die entsprechende Aktion ausführen. Sie können Ihren Bot so entwickeln, dass er Absichten anhand von Benutzereingaben erkennt und diese mit Aktionen verknüpft. 

Sie können mit der integrierten Erkennung für reguläre Ausdrücke des Bot Builder SDKs einen externen Dienst wie die LUIS-API aufrufen oder eine benutzerdefinierte Erkennung implementieren, um die Absicht des Benutzers zu ermitteln. [Erkennen der Benutzerabsicht](bot-builder-nodejs-recognize-intent-messages.md) enthält Beispiele, die veranschaulichen, wie Sie Ihrem Bot Erkennungen hinzufügen und mit diesen Aktionen auslösen.


## <a name="saving-state"></a>Speichern des Status

Wesentlich für gute Botentwicklung ist, den Unterhaltungskontext nachzuverfolgen, damit sich der Bot beispielsweise an die letzte Frage des Benutzers erinnert. Mit dem Bot Builder SDK erstellte Bots sind so konzipiert, dass sie statusfrei sind, sodass sie leicht skaliert werden können, um auf mehreren Serverknoten ausgeführt zu werden. Das Bot Framework bietet ein Speichersystem, das Botdaten speichert, sodass der Botwebdienst skaliert werden kann. Aus diesem Grund sollten Sie generell vermeiden, den Status mit einer globalen Variable oder einem Funktionsabschluss zu speichern. Dadurch entstehen Probleme, wenn Sie Ihren Bot skalieren wollen. Verwenden Sie stattdessen die folgenden Eigenschaften des [session][Session]-Objekts Ihres Bot, um Daten relativ zu einem Benutzer oder einer Unterhaltung zu speichern:

* **userData** speichert Informationen global für den Benutzer über alle Unterhaltungen hinweg.
* **conversationData** speichert Informationen global für eine einzelne Unterhaltung. Diese Daten sind für jeden in der Unterhaltung sichtbar, also gehen Sie beim Speichern von Daten in dieser Eigenschaft vorsichtig vor. Sie ist standardmäßig aktiviert und kann mit der Boteinstellung [persistConversationData][PersistConversationData] deaktiviert werden.
* **privateConversationData** speichert Informationen global für eine einzelne Unterhaltung, allerdings handelt es sich um private Daten des aktuellen Benutzers. Diese Daten umfassen alle Dialoge, sodass es nützlich ist, den temporären Status zu speichern, den Sie nach Unterhaltungsende zurücksetzen möchten.
* **dialogData** enthält Informationen für eine einzelne Dialoginstanz. Diese Daten werden benötigt, um temporäre Informationen zwischen den einzelnen Schritten eines [Wasserfalls](bot-builder-nodejs-dialog-waterfall.md) in einem Dialog zu speichern.

Verwendungsbeispiele für diese Eigenschaften zum Speichern und Abrufen von Daten finden Sie unter [Verwalten von Statusdaten](bot-builder-nodejs-state.md).

## <a name="natural-language-understanding"></a>Verstehen natürlicher Sprache

Mit Bot Builder können Sie LUIS verwenden, um Ihrem Bot mit der Klasse [LuisRecognizer][LuisRecognizer] ein natürliches Sprachverständnis hinzuzufügen. Sie können eine Instanz von **LuisRecognizer** hinzufügen, die auf Ihr veröffentlichtes Sprachmodell verweist, und dann Handler hinzufügen, um auf die Benutzeräußerungen zu reagieren. Wie LUIS in der Praxis funktioniert, sehen Sie in diesem 10-minütigen Tutorial:

* [Tutorial: Microsoft LUIS][LUISVideo] (Video)

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Übersicht über Dialoge](bot-builder-nodejs-dialog-overview.md)



[PersistConversationData]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.iuniversalbotsettings.html#persistconversationdata
[UniversalBot]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.universalbot.html
[ChatConnector]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.chatconnector.html
[ConsoleConnector]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.consoleconnector.html

[ChannelInspector]: ../bot-service-channel-inspector.md

[Session]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session.html
[SessionSend]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session#send

[triggerAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#triggeraction
[waterfall]: bot-builder-nodejs-prompts.md

[RespondMessages]:bot-builder-nodejs-use-default-message-handler.md

[LUISRecognizer]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.luisrecognizer
[LUISVideo]: https://vimeo.com/145499419

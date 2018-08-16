---
title: Senden und Empfangen von Aktivitäten | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mit einem Benutzer über verschiedene Kanäle mit dem Connector-Dienst über das Bot Builder SDK für .NET Informationen austauschen.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: c8919712331a5f78bdbc35f28adeab966435d737
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39302549"
---
# <a name="send-and-receive-activities"></a>Senden und Empfangen von Aktivitäten

Der Bot Framework Connector stellt eine einzelne REST-API bereit, mit der ein Bot über verschiedene Kanäle wie Skype, Slack, E-Mail etc. kommunizieren kann. Die Kommunikation zwischen Bot und Benutzer wird ermöglicht, indem Nachrichten vom Bot zum Kanal und vom Kanal zum Bot weitergeleitet werden. 

Dieser Artikel beschreibt, wie mit dem Connector über das Bot Builder SDK für .NET Informationen zwischen Bot und Benutzer über einen Kanal ausgetauscht werden. 

> [!NOTE]
> Es ist zwar möglich, einen Bot ausschließlich unter Verwendung der in diesem Artikel beschriebenen Techniken zu entwickeln, allerdings bietet das Bot Builder SDK zusätzliche Funktionen wie [Dialoge](bot-builder-dotnet-dialogs.md) und [FormFlow](bot-builder-dotnet-formflow.md), die die Verwaltung von Unterhaltungsfluss und -status optimieren sowie die Integration kognitiver Dienste wie Sprachverständnis vereinfachen.

## <a name="create-a-connector-client"></a>Erstellen eines Connector-Clients

Die Klasse [ConnectorClient][ConnectorClient] enthält die Methoden, mit denen ein Bot mit einem Benutzer über einen Kanal kommuniziert. Wenn Ihr Bot vom Connector ein <a href="https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Activity</a>-Objekt erhält, muss er mit der für diese Aktivität angegebenen `ServiceUrl` den Connector-Client erstellen, mit dem anschließend eine Antwort generiert wird. 

[!code-csharp[Create connector client](../includes/code/dotnet-send-and-receive.cs#createConnectorClient)]

> [!TIP]
> Da der Kanalendpunkt ggf. nicht stabil ist, sollte Ihr Bot die Kommunikation an den Endpunkt weiterleiten, den der Connector im `Activity`-Objekt angibt, sofern möglich (anstatt einen zwischengespeicherten Endpunkt zu verwenden). 
>
> Wenn Ihr Bot die Unterhaltung initiieren muss, kann er einen zwischengespeicherten Endpunkt für den angegebenen Kanal verwenden (da dieses Szenario kein eingehendes `Activity`-Objekt enthält). Allerdings müssen die zwischengespeicherten Endpunkte oft aktualisiert werden. 

## <a id="create-reply"></a> Erstellen einer Antwort

Der Connector verwendet ein [Activity](bot-builder-dotnet-activities.md)-Objekt, um Informationen zwischen Bot und Kanal (Benutzer) auszutauschen. Jede Aktivität enthält Informationen zum Weiterleiten der Nachricht an das passende Ziel sowie Informationen zum Ersteller der Nachricht (`From`-Eigenschaft), zum Kontext der Nachricht und zum Empfänger der Nachricht (`Recipient`-Eigenschaft).

Wenn Ihr Bot eine Aktivität vom Connector empfängt, gibt die Eigenschaft `Recipient` der eingehenden Aktivität die Identität des Bots in dieser Unterhaltung an. Da einige Kanäle (z.B. Slack) dem Bot eine neue Identität zuweisen, wenn er einer Unterhaltung hinzugefügt wird, muss der Bot immer den Wert der `Recipient`-Eigenschaft der eingehenden Aktivität als Wert der `From`-Eigenschaft in seiner Antwort verwenden.

Obwohl Sie das ausgehende `Activity`-Objekt selbst erstellen und initialisieren können, bietet das Bot Builder SDK eine einfachere Möglichkeit, eine Antwort zu erstellen. Mit der `CreateReply`-Methode der eingehenden Aktivität geben Sie einfach den Nachrichtentext für die Antwort an, und die ausgehende Aktivität wird mit den automatisch gefüllten Eigenschaften `Recipient`, `From` und `Conversation` erstellt.

[!code-csharp[Create reply](../includes/code/dotnet-send-and-receive.cs#createReply)]

## <a name="send-a-reply"></a>Senden einer Antwort

Nachdem Sie eine Antwort erstellt haben, können Sie sie durch Aufrufen der `ReplyToActivity`-Methode des Connector-Clients senden. Der Connector stellt die Antwort mit der entsprechenden Kanalsemantik bereit. 

[!code-csharp[Send reply](../includes/code/dotnet-send-and-receive.cs#sendReply)]

> [!TIP]
> Wenn Ihr Bot auf die Nachricht eines Benutzers antwortet, verwenden Sie immer die Methode `ReplyToActivity`.

## <a name="send-a-non-reply-message"></a>Senden einer (nicht als Antwort ausgelegten) Nachricht 

Wenn Ihr Bot Teil einer Unterhaltung ist, kann er eine Nachricht senden, die keine direkte Antwort auf eine Benutzernachricht ist, indem er die `SendToConversation`-Methode aufruft. 

[!code-csharp[Send non-reply message](../includes/code/dotnet-send-and-receive.cs#sendNonReplyMessage)]

Sie können die Methode `CreateReply` verwenden, um die neue Nachricht zu initialisieren. Dabei würden die Nachrichteneigenschaften `Recipient`, `From` und `Conversation` automatisch festgelegt werden. Alternativ können Sie die Methode `CreateMessageActivity` verwenden, um die neue Nachricht zu erstellen und alle Eigenschaftswerte selbst festzulegen.

> [!NOTE]
> Bot Framework legt keine Beschränkungen für die Anzahl von Nachrichten fest, die ein Bot senden darf. Die meisten Kanäle erzwingen jedoch Einschränkungsgrenzwerte, damit Bots keine große Anzahl von Nachrichten in kurzer Zeit senden. Wenn die Bots mehrere Nachrichten in schneller Folge senden, rendert der Kanal die Nachrichten außerdem ggf. nicht immer in der richtigen Reihenfolge.

## <a name="start-a-conversation"></a>Starten einer Unterhaltung

Es kann vorkommen, dass Ihr Bot eine Unterhaltung mit einem oder mehreren Benutzern initiieren muss. Sie können eine Unterhaltung starten, indem Sie entweder die Methode `CreateDirectConversation` (für eine private Unterhaltung mit einem einzelnen Benutzer) oder die Methode `CreateConversation` (für eine Gruppenunterhaltung mit mehreren Benutzern) aufrufen, um ein `ConversationAccount`-Objekt abzurufen. Erstellen Sie dann die Nachricht, und senden Sie sie durch Aufrufen der Methode `SendToConversation`. Um die `CreateDirectConversation`-Methode oder die `CreateConversation`-Methode zu verwenden, müssen Sie zuerst mit der Dienst-URL des Zielkanals den [Connector-Client erstellen](#create-a-connector-client). Die URL können Sie aus dem Cache abrufen, falls sie in früheren Nachrichten übermittelt wurde. 

> [!NOTE]
> Nicht alle Kanäle unterstützen Gruppenunterhaltungen. In der Dokumentation des jeweiligen Kanals erfahren Sie, ob er Gruppenunterhaltungen unterstützt.

Dieses Codebeispiel verwendet die `CreateDirectConversation`-Methode, um eine private Unterhaltung mit einem einzelnen Benutzer zu erstellen.

[!code-csharp[Start private conversation](../includes/code/dotnet-send-and-receive.cs#startPrivateConversation)]

Dieses Codebeispiel verwendet die `CreateConversation`-Methode, um eine Gruppenunterhaltung mit mehreren Benutzern zu erstellen.

[!code-csharp[Start group conversation](../includes/code/dotnet-send-and-receive.cs#startGroupConversation)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Übersicht über Aktivitäten](bot-builder-dotnet-activities.md)
- [Erstellen von Nachrichten](bot-builder-dotnet-create-messages.md)
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referenz zum Bot Builder SDK für .NET</a>
- <a href="https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Activity-Klasse</a>
- <a href="/dotnet/api/microsoft.bot.connector.connectorclient" target="_blank">ConnectorClient-Klasse</a>

[ConnectorClient]: /dotnet/api/microsoft.bot.connector.connectorclient

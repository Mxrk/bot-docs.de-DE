---
title: Schlüsselkonzepte im Bot Connector- und Bot State-Dienst | Microsoft-Dokumentation
description: Grundlegende Informationen zu Schlüsselkonzepten im Bot Connector- und Bot State-Dienst von Bot Framework.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 0bd98805023bc8f968ece591967ab2f4196531d4
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39303245"
---
# <a name="key-concepts"></a>Wichtige Begriffe

Sie können den Bot Connector-Dienst und den Bot State-Dienst verwenden, um mit Benutzern über mehrere Kanäle hinweg (Skype, E-Mail, Slack und vieles mehr) zu kommunizieren. Dieser Artikel bietet eine Einführung in Schlüsselkonzepte im Bot Connector- und Bot State-Dienst.

> [!IMPORTANT]
> Die Bot Framework State-Dienst-API wird nicht für Produktionsumgebungen empfohlen und kann in einem zukünftigen Release veraltet sein. Es wird empfohlen, dass Sie Ihren Botcode aktualisieren, um den In-Memory-Speicher für Testzwecke zu nutzen, oder verwenden Sie eine der **Azure-Erweiterungen** für Produktionsbots. Weitere Informationen finden Sie im Thema **Verwalten von Zustandsdaten** für [.NET](~/dotnet/bot-builder-dotnet-state.md) bzw. unter [Node](~/nodejs/bot-builder-nodejs-state.md)-Implementierung.

## <a name="bot-connector-service"></a>Bot Connector-Dienst

Der Bot Connector-Dienst ermöglicht es Ihrem Bot, Nachrichten mit im <a href="https://dev.botframework.com/" target="_blank">Bot Framework-Portal</a> konfigurierten Kanälen auszutauschen. Er verwendet die Branchenstandards REST und JSON über HTTPS und ermöglicht die Authentifizierung mit JWT Bearer-Token. Ausführliche Informationen zur Verwendung des Bot Connector-Diensts finden Sie unter [Authentifizierung](bot-framework-rest-connector-authentication.md) und in den restlichen Artikel in diesem Abschnitt.

### <a name="activity"></a>Aktivität

Der Bot Connector-Dienst tauscht durch Übergeben eines [Activity][Activity]-Objekts Informationen zwischen Bot und Kanal (Benutzer) aus. Der am häufigsten verwendete Aktivitätstyp ist **message**, aber es gibt andere Aktivitätstypen, die für die Kommunikation verschiedener Arten von Informationen an einen Bot oder Kanal verwendet werden können. Weitere Informationen zu Aktivitäten im Bot Connector-Dienst finden Sie unter [Überblick über Aktivitäten](bot-framework-rest-connector-activities.md).

## <a name="bot-state-service"></a>Bot State-Dienst

Der Bot State-Dienst ermöglicht Ihren Bots, Daten zu speichern und abzurufen, die einem Benutzer, einer Konversation oder einem bestimmten Benutzer innerhalb des Kontexts einer bestimmten Konversation zugeordnet sind. Er verwendet die Branchenstandards REST und JSON über HTTPS und ermöglicht die Authentifizierung mit JWT Bearer-Token. Alle Daten, die Sie unter Verwendung des Bot State-Diensts speichern, werden in Azure gespeichert und im Ruhezustand verschlüsselt.

Der Bot State-Dienst ist nur in Verbindung mit dem Bot Connector-Dienst nützlich. Das heißt, er kann nur zum Speichern von Daten verwendet werden, die Konversationen zugeordnet sind, die Ihr Bot mithilfe des Bot Connector-Diensts führt. Ausführliche Informationen zur Verwendung des Bot State-Diensts finden Sie unter [Authentifizierung](bot-framework-rest-connector-authentication.md) und [Verwalten von Statusdaten](bot-framework-rest-state.md).

## <a name="authentication"></a>Authentifizierung

Sowohl der Bot Connector-Dienst als auch der Bot State-Dienst ermöglichen die Authentifizierung mit JWT Bearer-Token. Ausführliche Informationen zum Authentifizieren von ausgehenden Anforderungen, die Ihr Bot an Bot Framework sendet und zum Authentifizieren eingehender Anforderungen, die Ihr Bot von Bot Framework erhält, und vieles mehr, finden Sie unter [Authentifizierung](bot-framework-rest-connector-authentication.md). 

## <a name="client-libraries"></a>Clientbibliotheken

Das Bot Framework stellt Clientbibliotheken bereit, die zum Erstellen von Bots in C# oder Node.js verwendet werden können. 

- Um einen Bot mit C# zu erstellen, verwenden Sie das [Bot Builder-SDK für C#](../dotnet/bot-builder-dotnet-overview.md). 
- Um einen Bot mit Node.js zu erstellen, verwenden Sie das [Bot Builder-SDK für Node.js](../nodejs/index.md). 

Zusätzlich zur Modellierung des Bot Connector- und des Bot State-Diensts bietet jedes Bot Builder-SDK auch ein leistungsstarkes System zur Erstellung von Dialogen, die Konversationslogik, integrierte Eingabeaufforderungen für einfache Dinge wie Ja/Nein, Zeichenfolgen, Zahlen und Aufzählungen, integrierte Unterstützung für leistungsstarke KI-Frameworks wie <a href="https://www.luis.ai/" target="_blank">LUIS</a> und vieles mehr einschließen. 

> [!NOTE]
> Alternativ zur Verwendung des C#- oder Node.js-SDKs können Sie Ihre eigene Clientbibliothek in der Sprache Ihrer Wahl erstellen, indem Sie die <a href="https://raw.githubusercontent.com/Microsoft/BotBuilder/master/CSharp/Library/Microsoft.Bot.Connector.Shared/Swagger/ConnectorAPI.json" target="_blank">Bot Connector-Swagger-Datei</a> und die <a href="https://raw.githubusercontent.com/Microsoft/BotBuilder/master/CSharp/Library/Microsoft.Bot.Connector.Shared/Swagger/StateAPI.json" target="_blank">Bot State-Swagger-Datei</a> verwenden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Weitere Informationen zum Erstellen von Bots mit dem Bot Connector- und dem Bot State-Dienst finden Sie in den Artikeln in diesem Abschnitt, beginnend mit [Authentifizierung](bot-framework-rest-connector-authentication.md). Wenn Sie Probleme oder Vorschläge in Bezug auf den Bot Connector- oder den Bot State-Dienst haben, finden Sie unter [Support](../bot-service-resources-links-help.md) eine Liste der verfügbaren Ressourcen. 

[Activity]: bot-framework-rest-connector-api-reference.md#activity-object
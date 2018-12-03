---
title: Bot Builder SDK für .NET | Microsoft-Dokumentation
description: Hier finden Sie Informationen zu den ersten Schritten mit dem Bot Builder SDK für .NET, einem leistungsstarken, benutzerfreundlichen Framework für das Erstellen von Bots.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 801925e2c179392804d9707e62bfaef082c8e81b
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49996698"
---
# <a name="bot-builder-sdk-for-net"></a>Bot Builder SDK für .NET

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-overview.md)
> - [Node.js](../nodejs/bot-builder-nodejs-overview.md)
> - [REST](../rest-api/bot-framework-rest-overview.md)

Das Bot Builder SDK für .NET ist ein leistungsstarkes Framework für die Erstellung von Bots, die sowohl freie Interaktionen als auch strukturiertere Unterhaltungen verarbeiten können, bei denen Benutzer mögliche Werte auswählen. Es ist einfach zu verwenden und nutzt C#, um .NET-Entwicklern eine vertraute Umgebung zum Schreiben von Bots zur Verfügung zu stellen.

Mit dem SDK können Sie Bots erstellen, die die folgenden SDK-Features nutzen: 

- Leistungsfähiges Dialogsystem mit einzelnen kombinierbaren Dialogen
- Integrierte Aufforderungen für einfache Eingaben wie Ja/Nein, Zeichenfolgen, Zahlen und Enumerationen
- Integrierte Dialoge, die leistungsfähige KI-Frameworks wie <a href="http://luis.ai" target="_blank">LUIS</a> nutzen
- FormFlow für die automatische Erstellung eines Bots (aus einer C#-Klasse), die den Benutzer durch die Konversation führt und nach Bedarf Hilfe, Navigation, Erläuterungen und Bestätigungen bereitstellt

> [!IMPORTANT]
> Am 31. Juli 2017 wurden wichtige Änderungen am Bot Framework-Sicherheitsprotokoll implementiert. Um zu verhindern, dass sich diese Änderungen negativ auf Ihren Bot auswirken, müssen Sie sicherstellen, dass Ihre Anwendung Bot Builder SDK v3.5 oder höher verwendet. Wenn Sie einen Bot mit einem SDK erstellt haben, das vor dem 5. Januar 2017 (Veröffentlichungsdatum von Bot Builder SDK v3.5) heruntergeladen wurde, führen Sie unbedingt ein Upgrade auf Bot Builder SDK v3.5 oder höher aus.

## <a name="get-the-sdk"></a>Abrufen des SDK

Das SDK ist als NuGet-Paket und Open Source-Lösung auf <a href="https://github.com/Microsoft/BotBuilder" target="_blank">GitHub</a> verfügbar.

> [!IMPORTANT]
> Für das Bot Builder SDK für .NET ist .NET Framework 4.6 oder höher erforderlich. Wenn Sie das SDK zu einem vorhandenen Projekt für eine niedrigere Version von .NET Framework hinzufügen, müssen Sie Ihr Projekt zuerst aktualisieren und auf .NET Framework 4.6 ausrichten.

Führen Sie zum Installieren des SDK in einem Visual Studio-Projekt die folgenden Schritte aus:

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektnamen, und wählen Sie **NuGet-Pakete verwalten...** aus.
2. Geben Sie auf der Registerkarte **Durchsuchen** im Suchfeld „Microsoft.Bot.Builder“ ein.
3. Wählen Sie in der Ergebnisliste **Microsoft.Bot.Builder** aus, klicken Sie auf **Installieren**, und akzeptieren Sie die Änderungen.

## <a name="get-code-samples"></a>Abrufen von Codebeispielen

Dieses SDK enthält [Beispielquellcode](bot-builder-dotnet-samples.md), der die Features des Bot Builder SDK für .NET nutzt.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Erstellen von Bots mit dem Bot Builder SDK für .NET finden Sie in den Artikeln in diesem Abschnitt, beginnend mit:

- [Schnellstart](bot-builder-dotnet-quickstart.md): Befolgen Sie die Anweisungen in diesem Schritt-für-Schritt-Tutorial zum schnellen Erstellen und Testen eines einfachen Bots.
- [Wichtige Konzepte:](bot-builder-dotnet-concepts.md) Dieser Artikel enthält Informationen zu wichtigen Konzepten im Bot Builder SDK für .NET.

Wenn Sie Probleme oder Vorschläge in Bezug auf das Bot Builder SDK haben, finden Sie unter [Support](../bot-service-resources-links-help.md) eine Liste der verfügbaren Ressourcen. 

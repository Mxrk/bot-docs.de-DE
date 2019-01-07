---
title: Erstellen eines Bots mit dem Bot Builder SDK für .NET | Microsoft-Dokumentation
description: Erstellen Sie einen Bot mit dem Bot Builder SDK für .NET, einem leistungsstarken Konstruktionsframework für Bots.
keywords: Bot Builder SDK, Erstellen eines Bots, Schnellstart, .NET, erste Schritte, C#-Bot
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: get-started-article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/19/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: d40b203ccd044992c026a592d5f86b0881754a41
ms.sourcegitcommit: f7a8f05fc05ff4a7212a437d540485bf68831604
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53735920"
---
# <a name="create-a-bot-with-the-bot-builder-sdk-for-net"></a>Erstellen eines Bots mit dem Bot Builder SDK für .NET
[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

In dieser Schnellstartanleitung werden die Schritte zum Erstellen eines Bots per C#-Vorlage und zum anschließenden Testen mit dem Bot Framework Emulator beschrieben.

## <a name="prerequisites"></a>Voraussetzungen
- Visual Studio [2017](https://www.visualstudio.com/downloads)
- Bot Builder SDK v4-Vorlage für [C#](https://aka.ms/bot-vsix)
- Bot Framework [Emulator](https://aka.ms/Emulator-wiki-getting-started)
- Kenntnisse von [ASP.Net Core](https://docs.microsoft.com/aspnet/core/) und asynchroner Programmierung in [C#](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/async/index)

## <a name="create-a-bot"></a>Erstellen eines Bots
Installieren Sie die Vorlage „BotBuilderVSIX.vsix“, die Sie im Abschnitt „Voraussetzungen“ heruntergeladen haben.

Erstellen Sie in Visual Studio mithilfe der Vorlage „Bot Builder Echo Bot V4“ ein neues Bot-Projekt.

![Visual Studio-Projekt](../media/azure-bot-quickstarts/bot-builder-dotnet-project.png)

> [!TIP] 
> Ändern Sie den Buildtyp des Projekts, falls erforderlich, in ``.Net Core 2.1``, und aktualisieren Sie ggf. die [NuGet-Pakete](https://docs.microsoft.com/en-us/nuget/quickstart/install-and-use-a-package-in-visual-studio).

Dank der Vorlage enthält Ihr Projekt sämtlichen Code, der zum Erstellen des Bots in dieser Schnellstartanleitung erforderlich ist. Sie müssen tatsächlich keinen zusätzlichen Code schreiben.

## <a name="start-your-bot-in-visual-studio"></a>Starten Ihres Bots in Visual Studio

Wenn Sie auf die Schaltfläche „Ausführen“ klicken, geschieht Folgendes: Visual Studio erstellt die Anwendung, stellt sie für „localhost“ bereit und startet den Webbrowser zum Anzeigen der Seite `default.htm` der Anwendung. Zu diesem Zeitpunkt wird Ihr Bot lokal ausgeführt.

## <a name="start-the-emulator-and-connect-your-bot"></a>Starten des Emulators und Herstellen einer Verbindung mit Ihrem Bot

Starten Sie im nächste Schritt den Emulator, und stellen Sie dann im Emulator eine Verbindung mit Ihrem Bot her:

1. Klicken Sie im Emulator auf der Registerkarte „Willkommen“ auf den Link **Bot öffnen**. 
2. Wählen Sie in dem Verzeichnis, in dem Sie die Visual Studio-Projektmappe erstellt haben, die BOT-Datei aus.

## <a name="interact-with-your-bot"></a>Interagieren mit Ihrem Bot

Senden Sie eine Nachricht an Ihren Bot. Dieser antwortet daraufhin mit einer Nachricht.

![Ausgeführter Emulator](../media/emulator-v4/emulator-running.png)

> [!NOTE]
> Wenn Sie sehen, dass die Nachricht nicht gesendet werden kann, müssen Sie Ihren Computer ggf. neu starten, weil ngrok die erforderlichen Berechtigungen für Ihr System noch nicht erhalten hat (einmaliger Vorgang).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Informationen zum Herstellen einer Verbindung mit einem remote gehosteten Bot finden Sie unter [Tunneling (ngrok)](https://github.com/Microsoft/BotFramework-Emulator/wiki/Tunneling-(ngrok)).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Funktionsweise von Bots](../v4sdk/bot-builder-basics.md) 

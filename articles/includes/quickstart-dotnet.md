---
ms.openlocfilehash: 2096aa342fe954b9f9f1d128bc080c0e0e6efdce
ms.sourcegitcommit: bdb981c0b11ee99d128e30ae0462705b2dae8572
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360800"
---
## <a name="prerequisites"></a>Voraussetzungen
- Visual Studio [2017](https://www.visualstudio.com/downloads)
- Bot Framework SDK v4-Vorlage für [C#](https://aka.ms/bot-vsix)
- Bot Framework [Emulator](https://aka.ms/Emulator-wiki-getting-started)
- Kenntnisse von [ASP.Net Core](https://docs.microsoft.com/aspnet/core/) und asynchroner Programmierung in [C#](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/async/index)

## <a name="create-a-bot"></a>Erstellen eines Bots
Installieren Sie die Vorlage „BotBuilderVSIX.vsix“, die Sie im Abschnitt „Voraussetzungen“ heruntergeladen haben.

Erstellen Sie in Visual Studio mithilfe der Vorlage „Bot Framework Echo Bot V4“ ein neues Bot-Projekt.

![Visual Studio-Projekt](~/media/azure-bot-quickstarts/bot-builder-dotnet-project.png)

> [!TIP] 
> Ändern Sie bei Bedarf den Buildtyp des Projekts in ``.Net Core 2.1``. Aktualisieren Sie außerdem ggf. die [NuGet-Pakete](https://docs.microsoft.com/en-us/nuget/quickstart/install-and-use-a-package-in-visual-studio) für `Microsoft.Bot.Builder`.

Dank der Vorlage enthält Ihr Projekt sämtlichen Code, der zum Erstellen des Bots in dieser Schnellstartanleitung erforderlich ist. Sie müssen tatsächlich keinen zusätzlichen Code schreiben.

## <a name="start-your-bot-in-visual-studio"></a>Starten Ihres Bots in Visual Studio

Wenn Sie auf die Schaltfläche „Ausführen“ klicken, geschieht Folgendes: Visual Studio erstellt die Anwendung, stellt sie für „localhost“ bereit und startet den Webbrowser zum Anzeigen der Seite `default.htm` der Anwendung. Zu diesem Zeitpunkt wird Ihr Bot lokal ausgeführt.

## <a name="start-the-emulator-and-connect-your-bot"></a>Starten des Emulators und Herstellen einer Verbindung mit Ihrem Bot

Starten Sie im nächste Schritt den Emulator, und stellen Sie dann im Emulator eine Verbindung mit Ihrem Bot her:

1. Klicken Sie im Emulator auf der Registerkarte „Willkommen“ auf den Link **Bot öffnen**. 
2. Wählen Sie in dem Verzeichnis, in dem Sie die Visual Studio-Projektmappe erstellt haben, die BOT-Datei aus.

## <a name="interact-with-your-bot"></a>Interagieren mit Ihrem Bot

Senden Sie eine Nachricht an Ihren Bot. Dieser antwortet daraufhin mit einer Nachricht.

![Ausgeführter Emulator](~/media/emulator-v4/emulator-running.png)

> [!NOTE]
> Wenn Sie sehen, dass die Nachricht nicht gesendet werden kann, müssen Sie Ihren Computer ggf. neu starten, weil ngrok die erforderlichen Berechtigungen für Ihr System noch nicht erhalten hat (einmaliger Vorgang).

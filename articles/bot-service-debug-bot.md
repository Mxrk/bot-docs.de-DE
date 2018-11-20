---
title: Debuggen eines Bots | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen mit Bot Service erstellten Bot debuggen.
author: v-ducvo
ms.author: v-ducvo
keywords: Bot Builder SDK, Bot debuggen, Bot testen, Bot-Emulator, Emulator
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/13/2018
ms.openlocfilehash: 997d907bfabb284e079f21437418645a7dac061e
ms.sourcegitcommit: 8b7bdbcbb01054f6aeb80d4a65b29177b30e1c20
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51645650"
---
# <a name="debug-a-bot"></a>Debuggen eines Bots

Dieser Artikel beschreibt das Debuggen Ihres Bots mithilfe einer integrierten Entwicklungsumgebung (IDE) wie Visual Studio oder Visual Studio Code und dem Bot Framework-Emulator. Mit den hier beschriebenen Methoden können Sie jeden beliebigen Bot lokal debuggen, im Schnellstart werden jedoch ein [C#](~/dotnet/bot-builder-dotnet-sdk-quickstart.md)-Bot und ein [JS](~/javascript/bot-builder-javascript-quickstart.md)-Bot verwendet.

## <a name="prerequisites"></a>Voraussetzungen 
- Laden Sie den [Bot Framework-Emulator](https://aka.ms/Emulator-wiki-getting-started) herunter, und installieren Sie ihn.
- Laden Sie [Visual Studio Code](https://code.visualstudio.com) oder [Visual Studio](https://www.visualstudio.com/downloads) (Community Edition oder höher) herunter, und installieren Sie das Programm.

### <a name="debug-a-javascript-bot-using-command-line-and-emulator"></a>Debuggen eines JavaScript-Bots über die Befehlszeile und mit dem Emulator

Führen Sie die folgenden Schritte aus, um einen JavaScript-Bot über die Befehlszeile auszuführen und mit dem Emulator zu testen:
1. Wechseln Sie an der Befehlszeile zu dem Verzeichnis mit Ihrem Botprojekt.
1. Starten Sie den Bot, indem Sie den Befehl **node app.js** ausführen.
1. Klicken Sie auf der Registerkarte „Willkommen“ des Emulators auf den Link **Bot öffnen**.
1. Wählen Sie in dem Verzeichnis, in dem Sie das Projekt erstellt haben, die BOT-Datei aus.
1. Senden Sie Ihrem Bot über den Emulator eine Nachricht (senden Sie z.B. die Nachricht „Hi“). 
1. Verwenden Sie die Bereiche **Inspektor** und **Protokoll** auf der rechten Seite des Emulatorfensters zum Debuggen Ihres Bots. Beispielsweise werden beim Klicken auf eine der Nachrichtenblasen (z.B. die Nachrichtenblase „Hi“ im Screenshot unten) die Details der Nachricht im Bereich **Inspektor** angezeigt. Hiermit können Sie Anforderungen und Antworten anzeigen, während Nachrichten zwischen dem Emulator und dem Bot ausgetauscht werden. Sie können auch auf einen der verknüpften Texte im Bereich **Protokoll** klicken, um die Details im Bereich **Inspektor** anzuzeigen.

   ![Bereich „Inspektor“ im Emulator](~/media/bot-service-debug-bot/emulator_inspector.png)

### <a name="debug-a-javascript-bot-using-breakpoints-in-visual-studio-code"></a>Debuggen eines JavaScript-Bots mithilfe von Haltepunkten in Visual Studio Code

In Visual Studio Code können Sie Breakpoints festlegen und den Bot im Debugmodus ausführen, um den Code schrittweise zu durchlaufen. Zum Festlegen von Haltepunkten in Visual Studio Code führen Sie die folgenden Schritte aus:

1. Starten Sie Visual Studio Code, und öffnen Sie den Ordner mit Ihrem Botprojekt.
2. Klicken Sie auf der Menüleiste auf **Debuggen** und dann auf **Debugging starten**. Wenn Sie zum Auswählen eines Laufzeitmoduls für das Ausführen des Codes aufgefordert werden, wählen Sie **Node.js** aus. Zu diesem Zeitpunkt wird der Bot lokal ausgeführt. 
<!--
   > [!NOTE]
   > If you get the "Value cannot be null" error, check to make sure your **Table Storage** setting is valid.
   > The **EchoBot** is default to using **Table Storage**. To use Table Storage in your bot, you need the table *name* and *key*. If you do not have a Table Storage instance ready, you can create one or for testing purposes, you can comment out the code that uses **TableBotDataStore** and uncomment the line of code that uses **InMemoryDataStore**. The **InMemoryDataStore** is intended for testing and prototyping only.
-->
3. Legen Sie Haltepunkte nach Bedarf fest. In Visual Studio Code können Sie Haltepunkte festlegen, indem Sie mit dem Mauszeiger auf die Spalte links neben den Zeilennummern zeigen. Ein kleiner roter Punkt wird angezeigt. Wenn Sie auf den Punkt klicken, wird der Haltepunkt festgelegt. Wenn Sie erneut auf den Punkt klicken, wird der Haltepunkt entfernt.

   ![Festlegen eines Haltepunkts in Visual Studio Code](~/media/bot-service-debug-bot/breakpoint-set.png)

4. Starten Sie den Bot Framework-Emulator, und stellen Sie eine Verbindung mit Ihrem Bot her, wie es im Abschnitt oben beschrieben ist. 
5. Senden Sie Ihrem Bot über den Emulator eine Nachricht (senden Sie z.B. die Nachricht „Hi“). Die Ausführung wird an der Zeile unterbrochen, an der Sie den Haltepunkt platziert haben.

   ![Debuggen in Visual Studio Code](~/media/bot-service-debug-bot/breakpoint-caught.png)

### <a name="debug-a-c-bot-using-breakpoints-in-visual-studio"></a>Debuggen eines C#-Bots mithilfe von Haltepunkten in Visual Studio

In Visual Studio (VS) können Sie Breakpoints festlegen und den Bot im Debugmodus ausführen, um den Code schrittweise zu durchlaufen. Zum Festlegen von Haltepunkten in Visual Studio führen Sie die folgenden Schritte aus:

1. Navigieren Sie zu Ihrem Botordner, und öffnen Sie die **SLN**-Datei. Damit wird die Projektmappe in Visual Studio geöffnet.
2. Klicken Sie auf der Menüleiste auf **Erstellen** und dann auf **Projektmappe erstellen**.
3. Klicken Sie im **Projektmappen-Explorer** auf **EchoWithCounterBot.cs**. In dieser Datei ist die Hauptlogik des Bots definiert. Legen Sie Breakpoints nach Bedarf fest. In Visual Studio können Sie Haltepunkte festlegen, indem Sie mit dem Mauszeiger auf die Spalte links neben den Zeilennummern zeigen. Ein kleiner roter Punkt wird angezeigt. Wenn Sie auf den Punkt klicken, wird der Haltepunkt festgelegt. Wenn Sie erneut auf den Punkt klicken, wird der Haltepunkt entfernt.
5. Klicken Sie auf der Menüleiste auf **Debuggen** und dann auf **Debugging starten**. Zu diesem Zeitpunkt wird der Bot lokal ausgeführt. 

<!--
   > [!NOTE]
   > If you get the "Value cannot be null" error, check to make sure your **Table Storage** setting is valid.
   > The **EchoBot** is default to using **Table Storage**. To use Table Storage in your bot, you need the table *name* and *key*. If you do not have a Table Storage instance ready, you can create one or for testing purposes, you can comment out the code that uses **TableBotDataStore** and uncomment the line of code that uses **InMemoryDataStore**. The **InMemoryDataStore** is intended for testing and prototyping only.
-->

   ![Festlegen eines Haltepunkts in Visual Studio](~/media/bot-service-debug-bot/breakpoint-set-vs.png)

7. Starten Sie den Bot Framework-Emulator, und stellen Sie eine Verbindung mit Ihrem Bot her, wie es im Abschnitt oben beschrieben ist. 
8. Senden Sie Ihrem Bot über den Emulator eine Nachricht (senden Sie z.B. die Nachricht „Hi“). Die Ausführung wird an der Zeile unterbrochen, an der Sie den Haltepunkt platziert haben.

   ![Debuggen in Visual Studio](~/media/bot-service-debug-bot/breakpoint-caught-vs.png)

::: moniker range="azure-bot-service-3.0" 

## <a id="debug-csharp-serverless"></a> Debuggen eines C\#-Functions-Bots mit Verbrauchsplan

Die serverlose C\#-Umgebung mit Verbrauchsplan in Bot Service ähnelt eher Node.js als einer typischen C\#-Anwendung, da sie – ähnlich wie das Node.js-Modul – einen Laufzeithost erfordert. In Azure ist die Laufzeit Teil der Hostingumgebung in der Cloud, doch müssen Sie diese Umgebung lokal auf Ihrem Desktop replizieren. 

### <a name="prerequisites"></a>Voraussetzungen

Bevor Sie Ihren C#-Bot mit Verbrauchsplan debuggen können, müssen Sie die folgenden Aufgaben ausführen.

- Laden Sie den Quellcode für Ihren Bot (aus Azure) herunter, wie es unter [Einrichten von Continuous Deployment](bot-service-continuous-deployment.md) beschrieben ist.
- Laden Sie den [Bot Framework-Emulator](https://aka.ms/Emulator-wiki-getting-started) herunter, und installieren Sie ihn.
- Installieren Sie die <a href="https://www.npmjs.com/package/azure-functions-cli" target="_blank">Azure Functions-Befehlszeilenschnittstelle</a>.
- Installieren Sie die <a href="https://github.com/dotnet/cli" target="_blank">DotNet-Befehlszeilenschnittstelle</a>.
  
Wenn Sie Ihren Code mithilfe von Haltepunkten in Visual Studio 2017 debuggen möchten, müssen Sie auch die folgenden Aufgaben ausführen.
  
- Laden Sie <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> (Community Edition oder höher) herunter, und installieren Sie das Programm.
- Laden Sie die <a href="https://visualstudiogallery.msdn.microsoft.com/e6bf6a3d-7411-4494-8a1e-28c1a8c4ce99" target="_blank">Visual Studio-Erweiterung Command Task Runner</a> herunter, und installieren Sie sie.

> [!NOTE]
> Visual Studio Code wird derzeit nicht unterstützt.

### <a name="debug-a-consumption-plan-c-functions-bot-using-the-emulator"></a>Debuggen eines C#-Functions-Bots mit Verbrauchsplan mithilfe des Emulators

Die einfachste Möglichkeit zum lokalen Debuggen Ihres Bots ist es, den Bot zu starten und dann eine Verbindung vom Bot Framework-Emulator herzustellen. 
Öffnen Sie zuerst eine Eingabeaufforderung, und navigieren Sie zu dem Ordner, in dem sich die Datei **project.json** in Ihrem Repository befindet. Führen Sie dann den Befehl `dotnet restore` aus, um die verschiedenen Pakete wiederherzustellen, auf die in Ihrem Bot verwiesen wird.

> [!NOTE]
> Bei Visual Studio 2017 hat sich die Art der Handhabung von Abhängigkeiten in Visual Studio geändert. Während Visual Studio 2015 **project.json** zur Handhabung von Abhängigkeiten verwendet, wird in Visual Studio 2017 ein **CSPROJ**-Modell beim Laden in Visual Studio verwendet. Wenn Sie Visual Studio 2017 verwenden, <a href="https://aka.ms/bf-debug-project">laden Sie diese **CSPROJ**-Datei</a> in den Ordner **/messages** in Ihrem Repository herunter, bevor Sie den Befehl `dotnet restore` ausführen.

![Eingabeaufforderung](~/media/bot-service-debug-bot/csharp-azureservice-debug-envconfig.png)

Führen Sie als Nächstes `debughost.cmd` zum Laden und Starten Ihres Bots aus. 

![Eingabeaufforderung zum Ausführen von „debughost.cmd“](~/media/bot-service-debug-bot/csharp-azureservice-debug-debughost.png)

Zu diesem Zeitpunkt wird der Bot lokal ausgeführt. Kopieren Sie im Konsolenfenster den Endpunkt, an dem der Debughost lauscht (in diesem Beispiel `http://localhost:3978`). Dann starten Sie den Bot Framework-Emulator, und fügen Sie den Endpunkt in die Adressleiste des Emulators ein. In diesem Beispiel müssen Sie außerdem `/api/messages` an den Endpunkt anfügen. Da Sie für das lokale Debuggen keine Sicherheit benötigen, können Sie die Felder **Microsoft-App-ID** und **Microsoft-App-Kennwort** leer lassen. Klicken Sie auf **Verbinden**, um eine Verbindung mit Ihrem Bot über den angegebenen Endpunkt herzustellen.

![Konfigurieren des Emulators](~/media/bot-service-debug-bot/mac-azureservice-emulator-config.png)

Nachdem Sie den Emulator mit Ihrem Bot verbunden haben, senden Sie eine Nachricht an Ihren Bot, indem Sie einen Text in das Textfeld eingeben, das sich am unteren Rand des Emulatorfensters befindet (d.h., wo **Geben Sie Ihre Nachricht ein...**  in der unteren linken Ecke angezeigt wird). Mithilfe der Bereiche **Protokoll** und **Inspektor** auf der rechten Seite des Emulatorfensters können Sie die Anforderungen und Antworten anzeigen, während Nachrichten zwischen dem Emulator und dem Bot ausgetauscht werden.

![Test über den Emulator](~/media/bot-service-debug-bot/mac-azureservice-debug-emulator.png)

Außerdem können Sie Protokolldetails im Konsolenfenster anzeigen.

![Konsolenfenster](~/media/bot-service-debug-bot/csharp-azureservice-debug-debughostlogging.png)

::: moniker-end

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Debuggen Ihres Bots mit Transkriptdateien](~/v4sdk/bot-builder-debug-transcript.md)

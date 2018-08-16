---
title: Debuggen eines mit Bot Service erstellten Bots | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen mit Bot Service erstellten Bot debuggen.
author: v-ducvo
ms.author: v-ducvo
keywords: Bot Builder SDK, Continuous Deployment, App Service, Emulator
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 07/13/2018
ms.openlocfilehash: 5e7fc6fa4fbce9b13d74d5dc7877de44840c51e6
ms.sourcegitcommit: dcbc8ad992a3e242a11ebcdf0ee99714d919a877
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/30/2018
ms.locfileid: "39352879"
---
# <a name="debug-a-bot-service-bot"></a>Debuggen eines Bot Service-Bots

Dieser Artikel beschreibt das Debuggen Ihres Bots mithilfe einer integrierten Entwicklungsumgebung (IDE) wie Visual Studio oder Visual Studio Code und dem Bot Framework-Emulator. In diesem Artikel wird der **EchoBot** verwendet, den Sie im Verlauf des Artikels [Erstellen eines Bots mit Bot Service](bot-service-quickstart.md) erstellt haben, doch können Sie diese Methoden zum lokalen Debuggen jedes Bots verwenden.

## <a name="debug-a-javascript-bot"></a>Debuggen eines JavaScript-Bots

Führen Sie die Schritte in diesem Abschnitt aus, um einen in JavaScript geschriebenen Bot zu debuggen.

### <a name="prerequisites"></a>Voraussetzungen

Bevor Sie Ihren JavaScript-Bot debuggen können, müssen Sie die folgenden Aufgaben ausführen.

- Laden Sie den Quellcode für Ihren Bot (aus Azure) herunter, wie es unter [Herunterladen von Bot-Quellcode](bot-service-build-download-source-code.md?#download-bot-source-code) beschrieben ist.
- Laden Sie den [Bot Framework-Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases) herunter, und installieren Sie ihn.
- Laden Sie einen Code-Editor wie <a href="https://code.visualstudio.com" target="_blank">Visual Studio Code</a> herunter, und installieren Sie ihn.

### <a name="debug-a-javascript-bot-using-command-line-and-emulator"></a>Debuggen eines JavaScript-Bots über die Befehlszeile und mit dem Emulator

Führen Sie die folgenden Schritte aus, um einen JavaScript-Bot über die Befehlszeile auszuführen und mit dem Emulator zu testen:
1. Wechseln Sie an der Befehlszeile zu dem Verzeichnis mit Ihrem Botprojekt.
2. Starten Sie den Bot, indem Sie den Befehl **node app.js** ausführen.
3. Starten Sie den Emulator, und stellen Sie eine Verbindung mit dem Endpunkte des Bots her (z.B. **http://localhost:3978/api/messages**). Wenn Sie den Bot zum ersten Mal ausführen, klicken Sie auf **Datei > Neuer Bot**, und folgen Sie den Anweisungen auf dem Bildschirm. Andernfalls klicken Sie auf **Datei > Bot öffnen**, um einen vorhandenen Bot zu öffnen. Da dieser Bot lokal auf Ihrem Computer ausgeführt wird, können Sie die Felder **MSA-App-ID** und **MSA-App-Kennwort** leer lassen. Weitere Informationen finden Sie unter [Debuggen mit dem Emulator](bot-service-debug-emulator.md).
4. Senden Sie Ihrem Bot über den Emulator eine Nachricht (senden Sie z.B. die Nachricht „Hi“). 
5. Verwenden Sie die Bereiche **Inspektor** und **Protokoll** auf der rechten Seite des Emulatorfensters zum Debuggen Ihres Bots. Beispielsweise werden beim Klicken auf eine der Nachrichtenblasen (z.B. die Nachrichtenblase „Hi“ im Screenshot unten) die Details der Nachricht im Bereich **Inspektor** angezeigt. Hiermit können Sie Anforderungen und Antworten anzeigen, während Nachrichten zwischen dem Emulator und dem Bot ausgetauscht werden. Sie können auch auf einen der verknüpften Texte im Bereich **Protokoll** klicken, um die Details im Bereich **Inspektor** anzuzeigen.

   ![Bereich „Inspektor“ im Emulator](~/media/bot-service-debug-bot/emulator_inspector.png)

### <a name="debug-a-javascript-bot-using-breakpoints-in-visual-studio-code"></a>Debuggen eines JavaScript-Bots mithilfe von Haltepunkten in Visual Studio Code

Mithilfe einer IDE wie Visual Studio Code können Sie Haltepunkte festlegen und den Bot im Debugmodus ausführen, um den Code schrittweise zu durchlaufen. Zum Festlegen von Haltepunkten in Visual Studio Code führen Sie die folgenden Schritte aus:

1. Starten Sie Visual Studio Code, und öffnen Sie den Ordner mit Ihrem Botprojekt.
2. Klicken Sie auf der Menüleiste auf **Debuggen** und dann auf **Debugging starten**. Wenn Sie zum Auswählen eines Laufzeitmoduls für das Ausführen des Codes aufgefordert werden, wählen Sie **Node.js** aus. Zu diesem Zeitpunkt wird der Bot lokal ausgeführt. 

   > [!NOTE]
   > Wenn der Fehler „Der Wert darf nicht NULL sein“ angezeigt wird, vergewissern Sie sich, dass Ihre Einstellung für **Table Storage** gültig ist.
   > Der **EchoBot** verwendet standardmäßig **Table Storage**. Um Table Storage in Ihrem Bot zu verwenden, benötigen Sie *Name* und *Schlüssel* der Tabelle. Wenn Ihnen keine Table Storage-Instanz bereitsteht, können Sie eine erstellen oder für Testzwecke den Code auskommentieren, der **TableBotDataStore** verwendet, und die Codezeile auskommentieren, die **InMemoryDataStore** verwendet. **InMemoryDataStore** ist nur für Tests und das Erstellen von Prototypen gedacht.

3. Legen Sie Haltepunkte nach Bedarf fest. In Visual Studio Code können Sie Haltepunkte festlegen, indem Sie mit dem Mauszeiger auf die Spalte links neben den Zeilennummern zeigen. Ein kleiner roter Punkt wird angezeigt. Wenn Sie auf den Punkt klicken, wird der Haltepunkt festgelegt. Wenn Sie erneut auf den Punkt klicken, wird der Haltepunkt entfernt.

   ![Festlegen eines Haltepunkts in Visual Studio Code](~/media/bot-service-debug-bot/breakpoint-set.png)

4. Starten Sie den Bot Framework-Emulator, und stellen Sie eine Verbindung mit Ihrem Bot her, wie es im Abschnitt oben beschrieben ist. 
5. Senden Sie Ihrem Bot über den Emulator eine Nachricht (senden Sie z.B. die Nachricht „Hi“). Die Ausführung wird an der Zeile unterbrochen, an der Sie den Haltepunkt platziert haben.

   ![Debuggen in Visual Studio Code](~/media/bot-service-debug-bot/breakpoint-caught.png)


## <a name="debug-a-c-bot"></a>Debuggen eines C#-Bots

Führen Sie die Schritte in diesem Abschnitt aus, um einen in C# geschriebenen Bot zu debuggen.


### <a name="prerequisites"></a>Voraussetzungen

Bevor Sie Ihren C#-Bot der Web-App debuggen können, müssen Sie die folgenden Aufgaben ausführen.

- Laden Sie den Quellcode für Ihren Bot (aus Azure) herunter, wie es unter [Herunterladen von Bot-Quellcode](bot-service-build-download-source-code.md?#download-bot-source-code) beschrieben ist.
- Laden Sie den [Bot Framework-Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases) herunter, und installieren Sie ihn.
- Laden Sie <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> (Community Edition oder höher) herunter, und installieren Sie das Programm.

### <a name="debug-a-c-bot-using-breakpoints-in-visual-studio"></a>Debuggen eines C#-Bots mithilfe von Haltepunkten in Visual Studio

Mithilfe einer IDE wie Visual Studio (VS) können Sie Haltepunkte festlegen und den Bot im Debugmodus ausführen, um den Code schrittweise zu durchlaufen. Zum Festlegen von Haltepunkten in Visual Studio führen Sie die folgenden Schritte aus:

1. Navigieren Sie zu Ihrem Botordner, und öffnen Sie die **SLN**-Datei. Damit wird die Projektmappe in Visual Studio geöffnet.
2. Klicken Sie auf der Menüleiste auf **Erstellen** und dann auf **Projektmappe erstellen**.
3. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Dialoge**, und klicken Sie auf **EchoDialog.cs**. In dieser Datei ist die Hauptlogik des Bots definiert.
4. Klicken Sie auf der Menüleiste auf **Debuggen** und dann auf **Debugging starten**. Zu diesem Zeitpunkt wird der Bot lokal ausgeführt. 

   > [!NOTE]
   > Wenn der Fehler „Der Wert darf nicht NULL sein“ angezeigt wird, vergewissern Sie sich, dass Ihre Einstellung für **Table Storage** gültig ist.
   > Der **EchoBot** verwendet standardmäßig **Table Storage**. Um Table Storage in Ihrem Bot zu verwenden, benötigen Sie *Name* und *Schlüssel* der Tabelle. Wenn Ihnen keine Table Storage-Instanz bereitsteht, können Sie eine erstellen oder für Testzwecke den Code auskommentieren, der **TableBotDataStore** verwendet, und die Codezeile auskommentieren, die **InMemoryDataStore** verwendet. **InMemoryDataStore** ist nur für Tests und das Erstellen von Prototypen gedacht.

5. Legen Sie Haltepunkte nach Bedarf fest. In Visual Studio können Sie Haltepunkte festlegen, indem Sie mit dem Mauszeiger auf die Spalte links neben den Zeilennummern zeigen. Ein kleiner roter Punkt wird angezeigt. Wenn Sie auf den Punkt klicken, wird der Haltepunkt festgelegt. Wenn Sie erneut auf den Punkt klicken, wird der Haltepunkt entfernt.

   ![Festlegen eines Haltepunkts in Visual Studio](~/media/bot-service-debug-bot/breakpoint-set-vs.png)

6. Starten Sie den Bot Framework-Emulator, und stellen Sie eine Verbindung mit Ihrem Bot her, wie es im Abschnitt oben beschrieben ist. 
7. Senden Sie Ihrem Bot über den Emulator eine Nachricht (senden Sie z.B. die Nachricht „Hi“). Die Ausführung wird an der Zeile unterbrochen, an der Sie den Haltepunkt platziert haben.

   ![Debuggen in Visual Studio](~/media/bot-service-debug-bot/breakpoint-caught-vs.png)

::: moniker range="azure-bot-service-3.0" 

## <a id="debug-csharp-serverless"></a> Debuggen eines C\#-Functions-Bots mit Verbrauchsplan

Die serverlose C\#-Umgebung mit Verbrauchsplan in Bot Service ähnelt eher Node.js als einer typischen C\#-Anwendung, da sie – ähnlich wie das Node.js-Modul – einen Laufzeithost erfordert. In Azure ist die Laufzeit Teil der Hostingumgebung in der Cloud, doch müssen Sie diese Umgebung lokal auf Ihrem Desktop replizieren. 

### <a name="prerequisites"></a>Voraussetzungen

Bevor Sie Ihren C#-Bot mit Verbrauchsplan debuggen können, müssen Sie die folgenden Aufgaben ausführen.

- Laden Sie den Quellcode für Ihren Bot (aus Azure) herunter, wie es unter [Einrichten von Continuous Deployment](bot-service-continuous-deployment.md) beschrieben ist.
- Laden Sie den [Bot Framework-Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases) herunter, und installieren Sie ihn.
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
::: moniker range="azure-bot-service-4.0" 

## <a id="debug-csharp-serverless"></a> Debuggen eines C\#-Functions-Bots mit Verbrauchsplan

Functions-Bot für Bot Builder SDK V4 ist bald verfügbar.

::: moniker-end

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Debuggen mit dem Emulator](bot-service-debug-emulator.md)

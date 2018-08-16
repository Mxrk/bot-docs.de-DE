---
title: Testen und Debuggen von Bots mit dem Bot Framework-Emulator | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit der Desktopanwendung Bot Framework-Emulator Bots prüfen, testen und debuggen.
keywords: Transkript, MSBot-Tool, Sprachdienste, Spracherkennung
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 04/30/2018
ms.openlocfilehash: bc1da99c7d0f7a6431ad0c2746b8583ef7bfbd5f
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39302592"
---
# <a name="debug-bots-with-the-bot-framework-emulator"></a>Debuggen von Bots mit dem Bot Framework-Emulator

Der Bot Framework-Emulator ist eine Desktopanwendung, mit der Botentwickler ihre Bots lokal oder remote testen und debuggen können. Mit dem Emulator können Sie mit Ihrem Bot chatten und die Nachrichten ansehen, die Ihr Bot sendet und empfängt. Der Emulator zeigt Nachrichten so an, wie sie in einer Webchat-Benutzeroberfläche erscheinen würden und protokolliert JSON-Anforderungen und -Antworten, während Sie Nachrichten mit Ihrem Bot austauschen. 

> [!TIP] 
> Bevor Sie Ihren Bot in der Cloud bereitstellen, führen Sie ihn lokal aus, und testen Sie ihn mit dem Emulator. Sie können Ihren Bot mit dem Emulator testen, auch wenn Sie ihn noch nicht im Bot Framework [registriert](~/bot-service-quickstart-registration.md) oder so konfiguriert haben, dass er in beliebigen Kanälen ausgeführt wird.

![Benutzeroberfläche des Emulators](media/emulator-v4/emulator-welcome.png)

## <a name="download-the-bot-framework-emulator"></a>Herunterladen des Bot Framework-Emulators

Downloadpakete für Mac, Windows und Linux sind auf der [GitHub-Releaseseite](https://github.com/Microsoft/BotFramework-Emulator/releases) verfügbar.

## <a name="connect-to-a-bot-running-on-local-host"></a>Verbinden mit auf lokalen Hosts ausgeführten Bots

![Emulatorendpunkte](media/emulator-v4/emulator-endpoint.png)

Um eine Verbindung mit einem lokal ausgeführten Bot herzustellen, wählen Sie oben links die Registerkarte „Bot Explorer“ aus. Klicken Sie unter der Registerkarte **Endpunkt** auf das Symbol **+**. Hier können Sie Ihren Endpunkt auf dem gleichen Port angeben, auf dem Ihr Bot lokal ausgeführt wird, um eine Verbindung herzustellen. Klicken Sie auf **Senden**, und Sie werden zu einem Livechatfenster weitergeleitet, in dem Sie mit Ihrem Bot interagieren können.

## <a name="view-detailed-message-activity-with-the-inspector"></a>Anzeigen detaillierter Nachrichtenaktivitäten im Inspector

![Nachrichtenaktivität im Emulator](media/emulator-v4/emulator-view-message-activity-02.png)

Sie können auf eine beliebige Nachrichtenblase im Unterhaltungsfenster klicken und die unformatierte JSON-Aktivität mit der Funktion **INSPECTOR** rechts neben dem Fenster überprüfen. Bei Auswahl wird die Nachrichtenblase gelb und das JSON-Aktivitätsobjekt wird links neben dem Chatfenster angezeigt. Diese JSON-Informationen enthalten wichtige Metadaten wie Kanal-ID, Aktivitätstyp, Unterhaltungs-ID, Textnachricht, Endpunkt-URL etc. Sie können die vom Benutzer gesendeten Aktivitäten sowie die Reaktionen des Bots ansehen. 

Sie können auch den [Channel Inspector](bot-service-channel-inspector.md) verwenden, um eine Vorschau der unterstützten Funktionen für bestimmte Kanäle anzuzeigen.


## <a name="save-and-load-conversations-with-bot-transcripts"></a>Speichern und Laden von Unterhaltungen mit Botprotokollen

Die Nachrichtenaktivität im Emulator kann als Protokoll gespeichert werden. Wählen Sie in einem geöffneten Livechatfenster **Protokoll speichern als** aus, um den Namen und den Speicherort der Protokolldatei auszuwählen. 

>[!TIP]
> Über die Schaltfläche **Neu starten** können Sie jederzeit eine Unterhaltung löschen und die Verbindung zum Bot neu starten.  

![Speichern von Protokollen im Emulator](media/emulator-v4/emulator-live-chat.png)

Um Protokolle zu laden, wählen Sie **Datei** --> **Protokolldatei öffnen** und dann das Protokoll aus. Ein neues Protokollfenster öffnet sich und zeigt die Nachrichtenaktivität im Ausgabefenster an. 

![Laden von Protokollen im Emulator](media/emulator-v4/emulator-load-transcript.png)

## <a name="author-transcripts-with-chatdown"></a>Erstellen von Protokollen mit Chatdown

Das Tool [Chatdown](https://github.com/Microsoft/botbuilder-tools/tree/master/Chatdown) ist ein Protokollgenerator, der mithilfe einer [Markdowndatei](https://daringfireball.net/projects/markdown/syntax) Aktivitätsprotokolle erzeugt. Sie können Ihre eigenen Protokolle komplett im Markdownformat erstellen und als **CHAT**-Datei speichern, um Protokolle zu generieren. Dies ist nützlich, um bei der Botentwicklung schnell Modellunterhaltungen zu erstellen.  

### <a name="prerequisites"></a>Voraussetzungen

- [Bot Framework-Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases) v.4 oder höher 
- [Node.js](https://nodejs.org/en/)
 
Chatdown steht als npm-Modul bereit, das Node.js erfordert. Um Chatdown zu installieren, installieren Sie es global auf Ihrem Rechner. 

```
npm install -g chatdown
```
### <a name="create-and-load-transcript-transcript-files"></a>Erstellen und Laden von Protokolldateien ###

Das folgende Beispiel zeigt, wie Sie eine **CHAT**-Datei erstellen. Diese Dateien im Markdownformat bestehen aus zwei Teilen:
- Dem Header, der die Teilnehmer der Unterhaltung (Benutzer, Bot) definiert
- Der Unterhaltung zwischen den Teilnehmern

```
user=John Doe
bot=Bot

bot: Hello!
user: hey
bot: [Typing][Delay=3000]
What can I do for you?
user: Actually nevermind, goodbye.
bot: bye!
```
[Klicken Sie hier](https://github.com/Microsoft/botbuilder-tools/tree/master/Chatdown/Examples), um weitere Beispiele für CHAT-Dateien anzuzeigen. 

Um die Protokolldatei mit dem Befehl **chatdown** in Ihrer CLI zu erzeugen, geben Sie den Namen der CHAT-Datei gefolgt von „>“ und dem Namen der Ausgabedatei des Protokolls ein. 

```
chatdown sample.chat > sample.transcript
```
## <a name="manage-bot-resources-with-the-msbot-tool"></a>Verwalten von Botressourcen mit dem MSBot-Tool

Das neue [MSBot](https://github.com/Microsoft/botbuilder-tools/tree/master/MSBot)-Tool ermöglicht das Erstellen einer **BOT**-Datei, in der Metadaten verschiedener Dienste, die Ihr Bot nutzt, an einem Ort gespeichert werden. Diese Datei erlaubt Ihrem Bot auch, sich über die CLI mit diesen Diensten zu verbinden. Das Tool als npm-Modul verfügbar. Führen Sie folgenden Befehl aus, um das Modul zu installieren:

```
npm install -g msbot 
```
![MSBot-CLI-Fenster](media/emulator-v4/msbot-cli-window.png)


Um eine BOT-Datei zu erstellen, geben Sie in der CLI **msbot init** gefolgt vom Namen Ihres Bots und den URL-Zielendpunkt ein, beispielsweise:

```shell
msbot init --name az-cli-bot --endpoint http://localhost:3984/api/messages
```
![BOT-Datei](media/emulator-v4/botfile-generated.png)

>**Hinweis:** Der für diesen Leitfaden verwendete Bot ist ein einfacher Echobot, der aus der Azure CLI-Boterweiterung generiert wurde. [Klicken Sie hier](https://github.com/Microsoft/botbuilder-tools/tree/master/AzureCli), um mehr über das Erstellen von Bots mit der Azure CLI zu erfahren. 

Mit der BOT-Datei können Sie Ihren Bot nun ganz einfach in den Emulator laden. Die BOT-Datei ist auch erforderlich, um verschiedene Endpunkte und Sprachkomponenten für Ihren Bot zu registrieren. 

![BOT-Datei – Dropdownliste](media/emulator-v4/bot-file-dropdown.png)

## <a name="add-language-services"></a>Hinzufügen von Sprachdiensten 

Sie können eine LUIS-App oder eine QnA Maker-Wissensdatenbank direkt über den Emulator in Ihrer BOT-Datei registrieren. Wenn die BOT-Datei geladen ist, wählen Sie die Schaltfläche „Dienste“ links im Emulatorfenster aus. Im Menü **Dienste** werden Optionen zum Hinzufügen von LUIS, QnA Maker, Dispatch, Endpunkten und Azure Bot Service angezeigt. 

Um eine LUIS-App hinzuzufügen, klicken Sie einfach im LUIS-Menü auf die Schaltfläche **+**, geben Sie Ihre Anmeldeinformationen für die LUIS-App ein, und klicken Sie auf **Senden**. Daraufhin wird die LUIS-App in der BOT-Datei registriert und der Dienst mit Ihrer Botanwendung verbunden. 

![Verbinden mit LUIS](media/emulator-v4/emulator-connect-luis-btn.png)

Um eine QnA Maker-Wissensbasis hinzuzufügen, klicken Sie einfach im QnA Maker-Menü auf die Schaltfläche **+**, geben Sie Ihre Anmeldeinformationen für die QnA Maker-Wissensdatenbank ein, und klicken Sie auf **Senden**. Ihre Wissensdatenbank wird nun in der BOT-Datei registriert und ist sofort verfügbar. 

![Verbinden mit QnA Maker](media/emulator-v4/emulator-connect-qna-btn.png)

Wenn ein Dienst verbunden ist, können Sie zu einem Livechatfenster zurückkehren und überprüfen, ob Ihre Dienste verbunden sind und ausgeführt werden. 

![Hergestellte Verbindung mit QnA Maker](media/emulator-v4/emulator-view-message-activity.png)

## <a name="inspect-language-services"></a>Prüfen von Sprachdiensten

Mit dem neuen Emulator v4 können Sie auch die JSON-Antworten von LUIS und QnA Maker überprüfen. Wenn Sie einen Bot mit einem verbundenen Sprachdienst verwenden, können Sie im Protokollfenster (LOG) unten rechts **trace** auswählen. Dieses neue Tool bietet auch Funktionen zur Aktualisierung Ihrer Sprachdienste direkt über den Emulator. 

![LUIS-Prüfung](media/emulator-v4/emulator-luis-inspector.png)

Bei einem verbundenen LUIS-Dienst gibt der Ablaufverfolgungslink **Luis Trace** (LUIS-Ablauf) an. Wenn Sie diese Option auswählen, sehen Sie die unverarbeitete Antwort Ihres LUIS-Dienstes, die die Absichten und Entitäten mit den angegebenen Bewertungen enthält. Sie haben auch die Möglichkeit, Absichten für Ihre Benutzeräußerungen erneut zuzuweisen. 

![QnA Maker-Prüfung](media/emulator-v4/emulator-qna-inspector.png)

Bei einem verbundenen QnA Maker-Dienst wird im Protokoll **QnA Maker Trace** (QnA Maker-Ablauf) angezeigt. Wenn Sie diese Option auswählen, können Sie eine Vorschau des Frage-Antwort-Paares dieser Aktivität zusammen mit einer Zuverlässigkeitsbewertung ansehen. Dann können Sie alternative Fragen für eine Antwort formulieren.

[!TIP]
> Diese Funktionen sind nur für Bots der SDK-Version 4 verfügbar. 


## <a name="speech-recognition"></a>Spracherkennung
Der Bot Framework-Emulator unterstützt Spracherkennung über die [Cognitive Services Speech-API](/azure/cognitive-services/Speech/home). So können Sie sprachaktivierte Bots und Cortana-Funktion via Sprache beim Entwickeln im Emulator trainieren. Der Bot Framework-Emulator stellt kostenlose Spracherkennung für bis zu drei Stunden pro Bot und Tag bereit. 

## <a id="ngrok"></a> Installieren und Konfigurieren von ngrok

Wenn Sie Windows verwenden, den Bot Framework-Emulator hinter einer Firewall oder einer anderen Netzwerkgrenze ausführen und eine Verbindung zu einem Bot herstellen möchten, der remote gehostet wird, müssen Sie die Tunnelingsoftware **ngrok** installieren und konfigurieren. Der Bot Framework-Emulator wird in die [ngrok][ngrokDownload]-Tunnelingsoftware (entwickelt von [inconshreveable][inconshreveable]) integriert und kann diese bei Bedarf automatisch starten.

Um **ngrok** unter Windows zu installieren und den Emulator zu konfigurieren, führen Sie diese Schritte aus: 

1. Laden Sie die ausführbare Datei [ngrok][ngrokDownload] auf Ihren lokalen Computer herunter.

2. Öffnen Sie im Emulator das Dialogfeld „App-Einstellungen“, geben Sie den ngrok-Pfad ein, wählen Sie aus, ob ngrok für lokale Adressen umgangen werden soll, und klicken Sie auf **Speichern**.

![ngrok-Pfad](media/emulator-v4/emulator-ngrok-path.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Der Bot Framework-Emulator ist eine Open Source-Ressource. Sie können [zur Entwicklung beitragen][EmulatorGithubContribute] und [ Fehler und Vorschläge][EmulatorGithubBugs] einreichen.


[EmulatorGithub]: https://github.com/Microsoft/BotFramework-Emulator
[EmulatorGithubContribute]: https://github.com/Microsoft/BotFramework-Emulator/wiki/How-to-Contribute
[EmulatorGithubBugs]: https://github.com/Microsoft/BotFramework-Emulator/wiki/Submitting-Bugs-%26-Suggestions

[ngrokDownload]: https://ngrok.com/
[inconshreveable]: https://inconshreveable.com/
[BotFrameworkDevPortal]: https://dev.botframework.com/


[EmulatorConnectPicture]: ~/media/emulator/emulator-connect_localhost_credentials.png
[EmulatorNgrokPath]: ~/media/emulator/emulator-configure_ngrok_path.png
[EmulatorNgrokMonitor]: ~/media/emulator/emulator-testbot-ngrok-monitoring.png
[EmulatorUI]: ~/media/emulator/emulator-ui-new.png

[TroubleshootingGuide]: ~/bot-service-troubleshoot-general-problems.md
[TroubleshootingAuth]: ~/bot-service-troubleshoot-authentication-problems.md
[NodeGetStarted]: ~/nodejs/bot-builder-nodejs-quickstart.md
[CSGetStarted]: ~/dotnet/bot-builder-dotnet-quickstart.md

---
title: Testen und Debuggen von Bots mit dem Bot Framework-Emulator | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit der Desktopanwendung Bot Framework-Emulator Bots prüfen, testen und debuggen.
keywords: Transkript, MSBot-Tool, Sprachdienste, Spracherkennung
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/13/2018
ms.openlocfilehash: f3a6a57a5fd01061493e5c216875f0c4210483f6
ms.sourcegitcommit: 8b7bdbcbb01054f6aeb80d4a65b29177b30e1c20
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51645610"
---
# <a name="debug-with-the-emulator"></a>Debuggen mit dem Emulator

Der Bot Framework-Emulator ist eine Desktopanwendung, mit der Botentwickler ihre Bots lokal oder remote testen und debuggen können. Mit dem Emulator können Sie mit Ihrem Bot chatten und die Nachrichten ansehen, die Ihr Bot sendet und empfängt. Der Emulator zeigt Nachrichten so an, wie sie in einer Webchat-Benutzeroberfläche erscheinen würden und protokolliert JSON-Anforderungen und -Antworten, während Sie Nachrichten mit Ihrem Bot austauschen. Bevor Sie Ihren Bot in der Cloud bereitstellen, führen Sie ihn lokal aus, und testen Sie ihn mit dem Emulator. Sie können Ihren Bot mit dem Emulator auch testen, wenn Sie ihn noch nicht mit Azure Bot Service [erstellt](./bot-service-quickstart.md) oder für die Ausführung in Kanälen konfiguriert haben.

## <a name="prerequisites"></a>Voraussetzungen
- Installieren des [Emulators](https://aka.ms/Emulator-wiki-getting-started)
- Installieren der [ngrok][ngrokDownload]-Tunnellingsoftware

## <a name="connect-to-a-bot-running-on-localhost"></a>Verbinden mit auf Localhost ausgeführten Bots

Klicken Sie zum Verbinden mit einem lokal ausgeführten Bot auf **Bot öffnen**, und wählen Sie die BOT-Datei aus. 

![Benutzeroberfläche des Emulators](media/emulator-v4/emulator-welcome.png)

## <a name="view-detailed-message-activity-with-the-inspector"></a>Anzeigen detaillierter Nachrichtenaktivitäten im Inspector

Senden Sie eine Nachricht an Ihren Bot, und der Bot sollte antworten. Sie können im Unterhaltungsfenster auf eine Nachrichtenblase klicken und die unformatierte JSON-Aktivität mit der Funktion **INSPECTOR** rechts neben dem Fenster überprüfen. Bei Auswahl wird die Nachrichtenblase gelb und das JSON-Aktivitätsobjekt wird links neben dem Chatfenster angezeigt. JSON-Informationen enthalten wichtige Metadaten wie Kanal-ID, Aktivitätstyp, Unterhaltungs-ID, Textnachricht, Endpunkt-URL etc. Sie können die vom Benutzer gesendeten Aktivitäten sowie die Reaktionen des Bots einsehen. 

![Nachrichtenaktivität im Emulator](media/emulator-v4/emulator-view-message-activity-02.png)

## <a name="save-and-load-conversations-with-bot-transcripts"></a>Speichern und Laden von Unterhaltungen mit Botprotokollen

Aktivitäten im Emulator können als Transkripte gespeichert werden. Speichern Sie das Transkript in einem offenen Livechat-Fenster mit der Option **Transkript speichern als** in der Transkriptdatei. Über die Schaltfläche **Neu starten** können Sie jederzeit eine Unterhaltung löschen und die Verbindung zum Bot neu starten.  

![Speichern von Protokollen im Emulator](media/emulator-v4/emulator-live-chat.png)

Zum Laden von Transkripten wählen Sie einfach **Datei > Transkriptdatei öffnen** und wählen dann das Transkript aus. Ein neues Protokollfenster öffnet sich und zeigt die Nachrichtenaktivität im Ausgabefenster an. 

![Laden von Protokollen im Emulator](media/emulator-v4/emulator-load-transcript.png)

## <a name="add-services"></a>Dienste hinzufügen 

Sie können eine LUIS-App, eine QnA Maker-Wissensdatenbank oder ein Dispatchmodell direkt über den Emulator Ihrer BOT-Datei hinzufügen. Wenn die BOT-Datei geladen ist, wählen Sie die Schaltfläche „Dienste“ links im Emulatorfenster aus. Im Menü **Dienste** werden Optionen zum Hinzufügen von LUIS, QnA Maker und Dispatch angezeigt. 

Zum Hinzufügen einer Dienst-App klicken Sie auf die Schaltfläche **+** und wählen dann den gewünschten Dienst aus. Sie werden aufgefordert, sich beim Azure-Portal anzumelden, um den Dienst der BOT-Datei hinzuzufügen und mit Ihrer Bot-Anwendung zu verbinden. 

![Verbinden mit LUIS](media/emulator-v4/emulator-connect-luis-btn.png)

Wenn ein Dienst verbunden ist, können Sie zu einem Livechatfenster zurückkehren und überprüfen, ob Ihre Dienste verbunden sind und ausgeführt werden. 

![Hergestellte Verbindung mit QnA Maker](media/emulator-v4/emulator-view-message-activity.png)

## <a name="inspect-services"></a>Dienste überprüfen

Mit dem neuen Emulator v4 können Sie auch die JSON-Antworten von LUIS und QnA Maker überprüfen. Wenn Sie einen Bot mit einem verbundenen Sprachdienst verwenden, können Sie im Protokollfenster (LOG) unten rechts **trace** auswählen. Dieses neue Tool bietet auch Funktionen zur Aktualisierung Ihrer Sprachdienste direkt über den Emulator. 

![LUIS-Prüfung](media/emulator-v4/emulator-luis-inspector.png)

Bei einem verbundenen LUIS-Dienst gibt der Ablaufverfolgungslink **Luis Trace** (LUIS-Ablauf) an. Wenn Sie diese Option auswählen, sehen Sie die unverarbeitete Antwort Ihres LUIS-Dienstes, die die Absichten und Entitäten mit den angegebenen Bewertungen enthält. Sie haben auch die Möglichkeit, Absichten für Ihre Benutzeräußerungen erneut zuzuweisen. 

![QnA Maker-Prüfung](media/emulator-v4/emulator-qna-inspector.png)

Bei einem verbundenen QnA Maker-Dienst wird im Protokoll **QnA Maker Trace** (QnA Maker-Ablauf) angezeigt. Wenn Sie diese Option auswählen, können Sie eine Vorschau des Frage-Antwort-Paares dieser Aktivität zusammen mit einer Zuverlässigkeitsbewertung ansehen. Dann können Sie alternative Fragen für eine Antwort formulieren.

## <a name="configure-ngrok"></a>ngrok konfigurieren

Wenn Sie Windows verwenden, den Bot Framework-Emulator hinter einer Firewall oder einer anderen Netzwerkgrenze ausführen und eine Verbindung zu einem Bot herstellen möchten, der remote gehostet wird, müssen Sie die Tunnelingsoftware **ngrok** installieren und konfigurieren. Der Bot Framework-Emulator ist umfassend in die ngrok-Tunnelingsoftware (entwickelt von [inconshreveable][inconshreveable]) integriert und kann diese bei Bedarf automatisch starten.

Öffnen Sie die **Emulatoreinstellungen**, geben Sie den ngrok-Pfad ein, wählen Sie aus, ob ngrok für lokale Adressen umgangen werden soll, und klicken Sie auf **Speichern**.

![ngrok-Pfad](media/emulator-v4/emulator-ngrok-path.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Der Bot Framework-Emulator ist eine Open Source-Ressource. Sie können [zur Entwicklung beitragen][EmulatorGithubContribute] und [ Fehler und Vorschläge][EmulatorGithubBugs] einreichen.



[EmulatorGithubContribute]: https://github.com/Microsoft/BotFramework-Emulator/wiki/How-to-Contribute
[EmulatorGithubBugs]: https://github.com/Microsoft/BotFramework-Emulator/wiki/Submitting-Bugs-%26-Suggestions

[ngrokDownload]: https://ngrok.com/
[inconshreveable]: https://inconshreveable.com/

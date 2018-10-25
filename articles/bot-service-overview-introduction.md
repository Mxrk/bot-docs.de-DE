---
title: Einführung in Azure Bot Service | Microsoft-Dokumentation
description: Erfahren Sie mehr über Bot Service, einen Dienst zum Erstellen, Verbinden, Testen, Bereitstellen, Überwachen und Verwalten von Bots.
keywords: Übersicht, Einführung, SDK, Überblick
author: Kaiqb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 10/08/2018
ms.openlocfilehash: 3ca80439a44ac7e715d19f8e47683ac9b5a5721a
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49998877"
---
::: moniker range="azure-bot-service-3.0"

# <a name="about-azure-bot-service"></a>Informationen zu Azure Bot Service

[!INCLUDE [pre-release-label](includes/pre-release-label-v3.md)]

Azure Bot Service bietet Tools zum Erstellen, Testen, Bereitstellen und Verwalten intelligenter Bots an einer zentralen Stelle. Über das vom SDK bereitgestellte modulare und erweiterbare Framework können Entwickler Vorlagen zum Erstellen von Bots nutzen, die Spracheingabe, Sprachverständnis, Fragen und Antworten und vieles mehr bieten.  

## <a name="what-is-a-bot"></a>Was ist ein Bot?
Ein Bot ist eine App, mit der Benutzer in Form einer Konversation mithilfe von Text, Grafiken (Karten) oder Sprache interagieren. Hierbei kann es sich um einen einfachen Frage-und-Antwort-Dialog handeln oder um einen komplexen Bot, der Benutzern eine Interaktion mit Diensten auf intelligente Weise anhand von Musterabgleich, Zustandsverfolgung und Techniken der künstlichen Intelligenz mit umfassender Integration in bestehende Unternehmensdienste bietet. 

## <a name="building-a-bot"></a>Erstellen eines Bots 
Sie können Ihre bevorzugte Entwicklungsumgebung oder Befehlszeilentools verwenden, um Ihren Bot in C# oder Node.js zu erstellen. Wir stellen Tools für verschiedene Phasen der Bot-Entwicklung bereit, die Sie für den Einstieg in das Erstellen von Bots verwenden können.    

![Botübersicht](media/bot-service-overview.png) 

## <a name="plan"></a>Plan 
Sehen Sie sich vor dem Schreiben von Code die [Entwurfsrichtlinien](bot-service-design-principles.md)  für Bots an, um Informationen zu bewährten Methoden und Anforderungen für Ihren Bot zu erhalten. Sie können einen einfachen Bot erstellen oder komplexere Funktionen einbinden, wie z.B. Spracheingabe, Sprachverständnis, Fragen und Antworten oder die Fähigkeit, Wissen aus verschiedenen Quellen zu extrahieren und intelligente Antworten bereitzustellen.  

> [!TIP]
> Erstellen Sie ein [Azure](https://portal.azure.com)-Konto. 

## <a name="build-your-bot"></a>Erstellen Ihres Bots 
Ihr Bot ist ein Webdienst, der eine Konversationsoberfläche implementiert und mit Bot Service kommuniziert. Sie können diese Lösung in einer beliebige Anzahl von Umgebungen und Sprachen erstellen, und wir bieten Tools für den einfachen Einstieg für Visual Studio oder Yeoman oder direkt im Azure-Portal. Nachstehend sind einige der Tools und Dienste aufgeführt, die Sie verwenden können.

> [!TIP]
> Erstellen Sie einen Bot über das [Azure-Portal](bot-service-quickstart.md). Fügen Sie bei Bedarf Botkomponenten hinzu, beispielsweise: 
> - Language Understanding [LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/home) 
> - [QnA Maker](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/home) – Wissensdatenbank zum Beantworten von Benutzerfragen  

## <a name="test-your-bot"></a>Testen Ihres Bots 
Bots sind komplexe Apps, bei denen viele verschiedene Komponenten zusammenarbeiten. Wie bei jeder komplexen App kann dies zu einigen interessanten Fehlern oder dazu führen, dass Ihr Bot ein anderes Verhalten als erwartet aufweist. Testen Sie Ihren Bot vor der Veröffentlichung.

> [!TIP] 
> - Testen des Bots im [Webchat](bot-service-manage-test-webchat.md) oder lokales Testen des Bots mit dem [Emulator](bot-service-debug-emulator.md)

## <a name="publish"></a>Veröffentlichen 
Wenn Sie bereit sind, veröffentlichen Sie Ihren Bot in Azure oder in Ihrem eigenen Webdienst oder Rechenzentrum. Sie können Continuous Deployment einrichten. Dies ermöglicht Ihnen das lokale Entwickeln Ihres Bots und ist nützlich, wenn Ihr Bot in eine Quellcodeverwaltung wie GitHub oder Visual Studio Team Services eingecheckt ist. Wenn Sie Änderungen in Ihrem Quellrepository einfügen, werden diese automatisch in Azure bereitgestellt.

> [!Tip]
> - [Herunterladen und erneutes Bereitstellen von Code in Azure](bot-service-build-download-source-code.md)

## <a name="connect"></a>Verbinden          
Verbinden Sie Ihren Bot mit Kanälen wie Facebook, Messenger, Kik, Skype, Slack, Microsoft Teams, Telegram, Text/SMS, Twilio, Cortana und Skype für mehr Interaktionen und das Erreichen von mehr Kunden.  
  
> [!TIP]
> - [Auswählen der hinzuzufügenden Kanäle](bot-service-manage-channels.md)


## <a name="evaluate"></a>Evaluate 
Verwenden Sie die im Azure-Portal gesammelten Daten, um Möglichkeiten zur Verbesserung der Funktionen und der Leistung Ihres Bots zu ermitteln. Sie können einen Servicelevel und Instrumentierungsdaten wie Datenverkehr, Latenz und Integrationen abrufen. Analytics bietet außerdem Berichterstellung zu Benutzer, Nachricht und Kanaldaten auf Konversationsebene.

> [!Tip]
> - [Sammeln von Analysen](bot-service-manage-analytics.md) 


::: moniker-end

::: moniker range="azure-bot-service-4.0"

# <a name="about-azure-bot-service"></a>Informationen zu Azure Bot Service

[!INCLUDE [pre-release-label](includes/pre-release-label.md)]

Azure Bot Service bietet Tools zum Erstellen, Testen, Bereitstellen und Verwalten intelligenter Bots an einer zentralen Stelle. Mithilfe des vom SDK bereitgestellten modularen und erweiterbaren Framework können Tools, Vorlagen und Entwickler von KI-Diensten Bots erstellen, die Spracheingabe verwenden, natürliche Sprache verstehen, Fragen und Antworten behandeln usw.

## <a name="what-is-a-bot"></a>Was ist ein Bot?
Bots bieten eine Benutzeroberfläche, die weniger an die Verwendung eines Computers und mehr an den Umgang mit einer Person erinnern – oder zumindest einem intelligenten Roboter. Sie können verwendet werden, um einfache, sich wiederholende Aufgaben, z.B. das Aufnehmen einer Tischreservierung zum Abendessen oder das Erfassen von Profilinformationen auf automatisierte Systeme zu übertragen, die u.U. keinen direkten Benutzereingriff mehr erfordern. Benutzer kommunizieren mit einem Bot mithilfe von Text, interaktiven Karten und Spracheingabe/-ausgabe. Bei einer Botinteraktion kann es sich um eine kurze Frage und Antwort oder auch um eine komplexe Konversation handeln, die auf intelligente Weise den Zugriff auf Dienste bereitstellt.

Bots sind mit modernen Webanwendungen im Internet vergleichbar, die APIs zum Senden und Empfangen von Nachrichten verwenden. Der Inhalt eines Bots fällt je nach Art des Bot sehr unterschiedlich aus. Moderne Botsoftware basiert auf einen Stapel von Technologien und Tools, um zunehmend komplexe Funktionen auf einer Vielzahl von Plattformen bereitzustellen. Allerdings kann ein einfacher Bot auch nur eine Nachricht erhalten und an den Benutzer zurückgeben, wobei sehr wenig Code zum Einsatz kommt. 

Bots sind zu denselben Dingen wie andere Arten von Software in der Lage – Lesen und Schreiben von Dateien, Verwenden von Datenbanken und APIs und Durchführen von regulären Berechnungsaufgaben. Was Bots einzigartig macht, ist die Verwendung von Mechanismen, die in der Regel auf die Kommunikation zwischen Menschen beschränkt sind. 

Bots umfassen in der Regel die folgenden Komponenten:
* Ein Webserver, in den meisten Fällen einer, der im öffentlichen Internet verfügbar ist
* Das Bot Builder-SDK und Bot Builder-Tools mit einer Schnittstelle für die Entwicklung von Bots
* Azure Cognitive Services 
* Azure Storage

## <a name="building-a-bot"></a>Erstellen eines Bots 

Azure Bot Service bietet einen integrierten Satz von Tools und Diensten, die diesen Prozess vereinfachen. Wählen Sie Ihre bevorzugte Entwicklungsumgebung oder Befehlszeilentools, um Ihren Bot in C#, JavaScript oder Typescript zu erstellen. (Java und Python sind auch in Kürze verfügbar!) Wir stellen Tools für verschiedene Phasen der Bot-Entwicklung bereit, die Sie für den Einstieg in das Erstellen von Bots verwenden können.

![Botübersicht](media/bot-service-overview.png) 

### <a name="plan"></a>Plan
Wie bei jeder Art von Software ist ein umfassendes Verständnis der Ziele, Prozesse und Benutzeranforderungen für das Erstellen eines erfolgreichen Bots wichtig. Sehen Sie sich vor dem Schreiben von Code die [Entwurfsrichtlinien](bot-service-design-principles.md)  für Bots an, um Informationen zu bewährten Methoden und Anforderungen für Ihren Bot zu erhalten. Sie können einen einfachen Bot erstellen oder komplexere Funktionen hinzufügen, beispielsweise Spracheingabe, das Verstehen natürlicher Sprache oder die Beantwortung von Fragen.

### <a name="build"></a>Entwickeln
Ihr Bot ist ein Webdienst, der eine Konversationsoberfläche implementiert und mit dem Bot Framework-Dienst kommuniziert, um Nachrichten und Ereignisse zu senden und zu empfangen. Sie können Bots in einer beliebigen Anzahl von Umgebungen und Sprachen erstellen. Sie können Ihre Botentwicklung im [Azure-Portal](bot-service-quickstart.md) starten oder [[C#](dotnet/bot-builder-dotnet-sdk-quickstart.md) | [JavaScript](javascript/bot-builder-javascript-quickstart.md)]-Vorlagen für die lokale Entwicklung verwenden.

Im Rahmen von Azure Bot Service bieten wir zusätzliche Komponenten an, die Sie zum Erweitern der Funktionalität Ihres Bots verwenden können.

| Feature | BESCHREIBUNG | Link |
| --- | --- | --- |
| Hinzufügen der Verarbeitung natürlicher Sprache | Ermöglichen Sie es Ihrem Bot, natürliche Sprache und Rechtschreibfehler zu verstehen, Sprachfunktionen zu verwenden sowie die Absichten des Benutzers zu erkennen. | Verwenden von [LUIS](~/v4sdk/bot-builder-howto-v4-luis.md) (Language Understanding Intelligent Service) 
| Beantworten von Fragen | Fügen Sie eine Wissensdatenbank hinzu, um Fragen von Benutzern auf natürlichere, mit einer Konversation vergleichbare Weise zu beantworten. | Verwenden von [QnA Maker](~/v4sdk/bot-builder-howto-qna.md) 
| Verwalten mehrerer Modelle | Wenn Sie mehrere Modelle nutzen (z. B. für LUIS und QnA Maker), können Sie auf intelligente Weise ermitteln, welches Modell während der Konversation des Bots verwendet werden sollte. | [Dispatch](~/v4sdk/bot-builder-tutorial-dispatch.md)-Tool|
| Hinzufügen von Karten und Schaltflächen | Verbessern Sie die Benutzerfreundlichkeit durch die Verwendung von Medien, die über reinen Text hinausgehen, beispielsweise Grafiken, Menüs und Karten. | [Hinzufügen von Karten](v4sdk/bot-builder-howto-add-media-attachments.md) |

> [!NOTE]
> Die Tabelle oben ist keine vollständige Liste. Weitere Informationen zur Botfunktionalität finden Sie in den Artikeln auf der linken Seite ab [Senden von Nachrichten](~/v4sdk/bot-builder-howto-send-messages.md).

Darüber hinaus bieten wir Befehlszeilentools, mit denen Sie Botressourcen erstellen, verwalten und testen können. Mithilfe dieser Tools können Sie eine Botkonfigurationsdatei verwalten, LUIS-Apps konfigurieren, eine QnA-Wissensdatenbank erstellen, eine Konversation simulieren und vieles mehr. Weitere Informationen finden Sie in der [Infodatei](https://aka.ms/botbuilder-tools-readme) zu Befehlszeilentools.

Sie haben auch Zugriff auf eine Vielzahl von [Beispielen](https://github.com/microsoft/botbuilder-samples), die zahlreiche der über das SDK verfügbaren Funktionen veranschaulichen. Diese Beispiele eignen sich hervorragend für Entwickler, die sich einen funktionsreicheren Ausgangspunkt wünschen.

### <a name="test"></a>Test 
Bots sind komplexe Apps, bei denen viele verschiedene Komponenten zusammenarbeiten. Wie bei jeder komplexen App kann dies zu einigen interessanten Fehlern oder dazu führen, dass Ihr Bot ein anderes Verhalten als erwartet aufweist. Testen Sie Ihren Bot vor der Veröffentlichung. Wir bieten mehrere Methoden zum Testen von Bots, bevor sie zur Verwendung freigegeben werden:

- Testen Sie Ihren Bot lokal mit dem [Emulator](bot-service-debug-emulator.md). Bot Framework Emulator ist eine eigenständige App, die nicht nur eine Chatschnittstelle, sondern auch Debugging- und Abfragetools bietet, um zu erfahren, wie Ihr Bot agiert und warum.  Der Emulator kann neben Ihrer in der Entwicklung befindlichen Botanwendung lokal ausgeführt werden. 
 
- Testen Sie Ihren Bot im [Web](bot-service-manage-test-webchat.md). Nachdem er über das Azure-Portal konfiguriert wurde, ist Ihr Bot auch über eine Webchat-Schnittstelle erreichbar. Die Webchat-Schnittstelle ist eine hervorragende Methode, um Testern und anderen Personen, die keinen Direktzugriff auf den ausgeführten Code des Bots haben, Zugriff auf Ihren Bot zu gewähren.

### <a name="publish"></a>Veröffentlichen 
Wenn Sie bereit sind, Ihren Bot im Web verfügbar zu machen, veröffentlichen Sie ihn in [Azure](bot-builder-howto-deploy-azure.md) oder in Ihrem eigenen Webdienst oder Rechenzentrum. Für das Aktivieren Ihres Bots auf Ihrer Website oder in Chatkanälen benötigen Sie zuerst eine Adresse im öffentlichen Internet.

### <a name="connect"></a>Verbinden          
Verbinden Sie Ihren Bot mit Kanälen wie Facebook, Messenger, Kik, Skype, Slack, Microsoft Teams, Telegram, Text/SMS, Twilio, Cortana und Skype. Bot Framework übernimmt den Großteil der erforderlichen Aufgaben für das Senden und Empfangen von Nachrichten über alle diese verschiedenen Plattformen. Ihre Botanwendung empfängt unabhängig von Anzahl und Typ der Kanäle, mit denen sie verbunden ist, einen einheitlichen, normalisierten Nachrichtenstrom. Informationen zum Hinzufügen von Kanälen finden Sie im Thema [Kanäle](bot-service-manage-channels.md).

### <a name="evaluate"></a>Evaluate 
Verwenden Sie die im Azure-Portal gesammelten Daten, um Möglichkeiten zur Verbesserung der Funktionen und der Leistung Ihres Bots zu ermitteln. Sie können einen Servicelevel und Instrumentierungsdaten wie Datenverkehr, Latenz und Integrationen abrufen. Analytics bietet außerdem Berichterstellung zu Benutzer, Nachricht und Kanaldaten auf Konversationsebene. Weitere Informationen hierzu finden Sie im Artikel zum [Erfassen von Analysen](bot-service-manage-analytics.md).


## <a name="next-steps"></a>Nächste Schritte
Sehen Sie sich diese [Fallstudien](https://azure.microsoft.com/services/bot-service/) zu Bots an, oder klicken Sie auf den folgenden Link, um einen Bot zu erstellen.
> [!div class="nextstepaction"]
> [Erstellen eines Bots](bot-service-quickstart.md)

::: moniker-end

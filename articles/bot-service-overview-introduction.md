---
title: Informationen zu Bot Service | Microsoft-Dokumentation
description: Erfahren Sie mehr über Bot Service, einen Dienst zum Erstellen, Verbinden, Testen, Bereitstellen, Überwachen und Verwalten von Bots.
keywords: Übersicht, Einführung, SDK, Überblick
author: Kaiqb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 05/03/2018
ms.openlocfilehash: b6326ac152112ff1df01470db1f525d4bf241af4
ms.sourcegitcommit: 67445b42796d90661afc643c6bb6533e9a662cbc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39574606"
---
::: moniker range="azure-bot-service-3.0"

# <a name="azure-bot-service"></a>Azure Bot Service

[!INCLUDE [pre-release-label](includes/pre-release-label-v3.md)]

Azure Bot Service bietet Tools zum Erstellen, Testen, Bereitstellen und Verwalten intelligenter Bots an einer zentralen Stelle. Über das vom SDK bereitgestellte modulare und erweiterbare Framework können Entwickler Vorlagen zum Erstellen von Bots nutzen, die Spracheingabe, Sprachverständnis, Fragen und Antworten und vieles mehr bieten.  

## <a name="what-is-a-bot"></a>Was ist ein Bot?
Ein Bot ist eine App, mit der Benutzer in Form einer Konversation mithilfe von Text, Grafiken (Karten) oder Sprache interagieren. Hierbei kann es sich um einen einfachen Frage-und-Antwort-Dialog handeln oder um einen komplexen Bot, der Benutzern eine Interaktion mit Diensten auf intelligente Weise anhand von Musterabgleich, Zustandsverfolgung und Techniken der künstlichen Intelligenz mit umfassender Integration in bestehende Unternehmensdienste bietet. Sehen Sie sich [Fallstudien](https://azure.microsoft.com/services/bot-service/) zu Bots an.  

## <a name="building-a-bot"></a>Erstellen eines Bots 
Sie können Ihre bevorzugte Entwicklungsumgebung oder Befehlszeilentools verwenden, um Ihren Bot in C# oder Node.js zu erstellen. Wir stellen Tools für verschiedene Phasen der Bot-Entwicklung bereit, die Sie für den Einstieg in das Erstellen von Bots verwenden können.    

![Botübersicht](media/bot-service-overview.png) 

## <a name="plan"></a>Plan 
Sehen Sie sich vor dem Schreiben von Code die [Entwurfsrichtlinien](bot-service-design-principles.md)  für Bots an, um Informationen zu bewährten Methoden und Anforderungen für Ihren Bot zu erhalten. Sie können einen einfachen Bot erstellen oder komplexere Funktionen einbinden, wie z.B. Spracheingabe, Sprachverständnis, Fragen und Antworten oder die Fähigkeit, Wissen aus verschiedenen Quellen zu extrahieren und intelligente Antworten bereitzustellen.  

> [!TIP] 
>
> Installieren Sie die benötigte Vorlage:
>  - Bot Builder .NET SDK v3 [VSIX-Vorlage](https://marketplace.visualstudio.com/items?itemName=BotBuilder.BotBuilderV3) 
>  - Bot Builder Node.js SDK v3 [Yeoman-Vorlage](https://www.npmjs.com/package/generator-botbuilder) 
>
> Installieren Sie Tools:
> - Laden Sie [CLI-Tools](https://github.com/Microsoft/botbuilder-tools) zum Erstellen und Verwalten von Botobjekten herunter. Mithilfe dieser Tools können Sie die Bot-Konfigurationsdatei, LUIS-App, QnA-Wissensdatenbank und vieles mehr über die Befehlszeile verwalten. Weitere Informationen finden Sie in der [Infodatei](https://github.com/Microsoft/botbuilder-tools/blob/master/README.md).
> - [Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases) zum Testen des Bots
>
> Verwenden Sie bei Bedarf Botkomponenten wie beispielsweise:  
> - [LUIS](https://www.luis.ai/) zum Hinzufügen von Sprachverständnis zu Bots
> - [QnA Maker](https://qnamaker.ai/) zum Beantworten von Benutzerfragen auf natürlichere Weise in Form einer Konversation
> - [Spracheingabe](https://azure.microsoft.com/services/cognitive-services/speech/) zum Umwandeln von Audio in Text, Verstehen der Absicht und Rückumwandeln von Text in Sprache  
> - [Rechtschreibprüfung](https://azure.microsoft.com/services/cognitive-services/spell-check/) zum Korrigieren von Rechtschreibfehlern sowie Erkennen der Unterschiede zwischen Namen, Markennamen und Jargon 
> - [Cognitive Services](bot-service-concept-intelligence.md) für verschiedene andere intelligente Komponenten 


## <a name="build-your-bot"></a>Erstellen Ihres Bots 
Ihr Bot ist ein Webdienst, der eine Konversationsoberfläche implementiert und mit Bot Service kommuniziert. Sie können diese Lösung in einer beliebige Anzahl von Umgebungen und Sprachen erstellen, und wir bieten Tools für den einfachen Einstieg für Visual Studio oder Yeoman oder direkt im Azure-Portal. Nachstehend sind einige der Tools und Dienste aufgeführt, die Sie verwenden können.

> [!TIP]
>
> Erstellen Sie einen Bot mit dem [SDK](~/dotnet/bot-builder-dotnet-quickstart.md), [Azure-Portal](bot-service-quickstart.md), oder verwenden Sie [CLI-Tools](~/bot-builder-create-templates.md).
>
> Fügen Sie Komponenten hinzu: 
> - Fügen Sie das Language Understanding-Modell [LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/home) hinzu. 
> - Fügen Sie eine [QnA Maker](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/home)-Wissensdatenbank zum Beantworten von Benutzerfragen hinzu.  
> - Verbessern Sie die Benutzerfreundlichkeit mit Karten, Spracheingabe oder Übersetzung. 
> - Fügen Sie Ihrem Bot mithilfe des Bot Builder SDK Logik hinzu.   

## <a name="test-your-bot"></a>Testen Ihres Bots 
Bots sind komplexe Apps, bei denen viele verschiedene Komponenten zusammenarbeiten. Wie bei jeder komplexen App kann dies zu einigen interessanten Fehlern oder dazu führen, dass Ihr Bot ein anderes Verhalten als erwartet aufweist. Testen Sie Ihren Bot vor der Veröffentlichung.

> [!TIP] 
>
> - [Testen des Bots mit dem Emulator](bot-service-debug-emulator.md)
> - [Testen des Bots im Webchat](bot-service-manage-test-webchat.md)

## <a name="publish"></a>Veröffentlichen 
Wenn Sie bereit sind, veröffentlichen Sie Ihren Bot in Azure oder in Ihrem eigenen Webdienst oder Rechenzentrum. Sie können Continuous Deployment einrichten. Dies ermöglicht Ihnen das lokale Entwickeln Ihres Bots und ist nützlich, wenn Ihr Bot in eine Quellcodeverwaltung wie GitHub oder Visual Studio Team Services eingecheckt ist. Wenn Sie Änderungen wieder in Ihr Quellrepository einchecken, werden diese automatisch in Azure bereitgestellt.

> [!Tip]
>
> - [Bereitstellen in Azure](bot-service-build-continuous-deployment.md)

## <a name="connect"></a>Verbinden          
Verbinden Sie Ihren Bot mit Kanälen wie Facebook, Messenger, Kik, Skype, Slack, Microsoft Teams, Telegram, Text/SMS, Twilio, Cortana und Skype für mehr Interaktionen und das Erreichen von mehr Kunden.  
  
> [!TIP]
>
> - [Auswählen der hinzuzufügenden Kanäle](bot-service-manage-channels.md)


## <a name="evaluate"></a>Evaluate 
Verwenden Sie die im Azure-Portal gesammelten Daten, um Möglichkeiten zur Verbesserung der Funktionen und der Leistung Ihres Bots zu ermitteln. Sie können einen Servicelevel und Instrumentierungsdaten wie Datenverkehr, Latenz und Integrationen abrufen. Analytics bietet außerdem Berichterstellung zu Benutzer, Nachricht und Kanaldaten auf Konversationsebene.

> [!Tip]
>
> - [Sammeln von Analysen](bot-service-manage-analytics.md) 


::: moniker-end

::: moniker range="azure-bot-service-4.0"

# <a name="azure-bot-service"></a>Azure Bot Service

[!INCLUDE [pre-release-label](includes/pre-release-label.md)]

Azure Bot Service bietet Tools zum Erstellen, Testen, Bereitstellen und Verwalten intelligenter Bots an einer zentralen Stelle. Über das vom SDK bereitgestellte modulare und erweiterbare Framework können Entwickler Vorlagen zum Erstellen von Bots nutzen, die Spracheingabe, Sprachverständnis, Fragen und Antworten und vieles mehr bieten.  

## <a name="what-is-a-bot"></a>Was ist ein Bot?
Ein Bot ist eine App, mit der Benutzer in Form einer Konversation mithilfe von Text, Grafiken (Karten) oder Sprache interagieren. Hierbei kann es sich um einen einfachen Frage-und-Antwort-Dialog handeln oder um einen komplexen Bot, der Benutzern eine Interaktion mit Diensten auf intelligente Weise anhand von Musterabgleich, Zustandsverfolgung und Techniken der künstlichen Intelligenz mit umfassender Integration in bestehende Unternehmensdienste bietet. Sehen Sie sich [Fallstudien](https://azure.microsoft.com/services/bot-service/) zu Bots an.  

## <a name="building-a-bot"></a>Erstellen eines Bots 
Sie können Ihre bevorzugte Entwicklungsumgebung oder Befehlszeilentools verwenden, um Ihren Bot in C#, JavaScript, Java und Python zu erstellen. Wir stellen Tools für verschiedene Phasen der Bot-Entwicklung bereit, die Sie für den Einstieg in das Erstellen von Bots verwenden können.    

![Botübersicht](media/bot-service-overview.png) 

## <a name="plan"></a>Plan 
Sehen Sie sich vor dem Schreiben von Code die [Entwurfsrichtlinien](bot-service-design-principles.md)  für Bots an, um Informationen zu bewährten Methoden und Anforderungen für Ihren Bot zu erhalten. Sie können einen einfachen Bot erstellen oder komplexere Funktionen einbinden, wie z.B. Spracheingabe, Sprachverständnis, Fragen und Antworten oder die Fähigkeit, Wissen aus verschiedenen Quellen zu extrahieren und intelligente Antworten bereitzustellen. Für den Einstieg können Sie die [CLI-Tools](~/bot-builder-create-templates.md), [Azure-Portal](bot-service-quickstart.md) oder die unten aufgeführten Vorlagen verwenden.

**Aufrufen der benötigten Vorlage**

| .NET | JavaScript | 
| --- | --- | 
| [VSIX-Vorlage](https://marketplace.visualstudio.com/items?itemName=BotBuilder.botbuilderv4) | [Yeoman-Vorlage](https://www.npmjs.com/package/generator-botbuilder). Verwenden Sie @preview zum Abrufen der v4-Vorlage. |

**Erkunden oder Installieren der Tools bei Bedarf**

- Laden Sie [CLI-Tools](https://github.com/Microsoft/botbuilder-tools) zum Erstellen und Verwalten von Botobjekten herunter. Mithilfe dieser Tools können Sie die Bot-Konfigurationsdatei, LUIS-App, QnA-Wissensdatenbank und vieles mehr über die Befehlszeile verwalten. Weitere Informationen finden Sie in der [Infodatei](https://github.com/Microsoft/botbuilder-tools/blob/master/README.md).
- [Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases) zum Testen des Bots
- [LUIS](https://www.luis.ai/) zum Hinzufügen natürlicher Sprache zu Bots
- [QnA Maker](https://qnamaker.ai/) zum Beantworten von Benutzerfragen auf natürlichere Weise in Form einer Konversation


## <a name="build-your-bot"></a>Erstellen Ihres Bots 
Ihr Bot ist ein Webdienst, der eine Konversationsoberfläche implementiert und mit Bot Service kommuniziert. Sie können diese Lösung in einer beliebige Anzahl von Umgebungen und Sprachen erstellen, und wir bieten Tools für den einfachen Einstieg für Visual Studio oder Yeoman oder direkt im Azure-Portal. Nachstehend sind einige der Tools und Dienste aufgeführt, die Sie verwenden können.

Einige der vielen verfügbaren Komponenten sind:

| Komponenten | BESCHREIBUNG |
| --- | --- |
| [LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/home) | Hinzufügen von Sprachverständnis |
| [QnA Maker](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/home)  | Hinzufügen einer Wissensdatenbank zum Beantworten von Benutzerfragen |
| [Dispatch-Tool](~/v4sdk/bot-builder-tutorial-dispatch.md) | Bei Verwendung mehrerer Modelle wird auf intelligente Weise bestimmt, wann welches zu verwenden ist |
| [Rich-Media](v4sdk/bot-builder-howto-add-media-attachments.md) | Verbessern der Benutzerfreundlichkeit mit Media Cards oder Spracheingabe |
| [Spracheingabe](https://azure.microsoft.com/services/cognitive-services/speech/) | Umwandeln von Audio in Text, Verstehen der Absicht und Rückumwandeln von Text in Sprache |
| [Rechtschreibprüfung](https://azure.microsoft.com/services/cognitive-services/spell-check/) | Korrigieren von Rechtschreibfehlern, Erkennen der Unterschiede zwischen Namen, Markennamen und Jargon |

> [!NOTE]
> Die Tabelle oben ist keine vollständige Liste. Weitere Informationen zur Botfunktionalität finden Sie in den Artikeln auf der linken Seite ab [Senden von Nachrichten](v4sdk/bot-builder-howto-send-messages.md).

## <a name="test-your-bot"></a>Testen Ihres Bots 
Bots sind komplexe Apps, bei denen viele verschiedene Komponenten zusammenarbeiten. Wie bei jeder komplexen App kann dies zu einigen interessanten Fehlern oder dazu führen, dass Ihr Bot ein anderes Verhalten als erwartet aufweist. Testen Sie Ihren Bot vor der Veröffentlichung. 

[Testen Sie den Bot mit dem Emulator](bot-service-debug-emulator.md), oder [testen Sie den Bot im Webchat](bot-service-manage-test-webchat.md).

## <a name="publish"></a>Veröffentlichen 
Wenn Sie bereit sind, veröffentlichen Sie Ihren Bot in Azure oder in Ihrem eigenen Webdienst oder Rechenzentrum. Sie können Continuous Deployment einrichten. Dies ermöglicht Ihnen das lokale Entwickeln Ihres Bots und ist nützlich, wenn Ihr Bot in eine Quellcodeverwaltung wie GitHub oder Visual Studio Team Services eingecheckt ist. Wenn Sie Änderungen wieder in Ihr Quellrepository einchecken, werden diese automatisch in Azure bereitgestellt.

[Bereitstellen in Azure](bot-service-build-continuous-deployment.md) über Continuous Deployment

## <a name="connect"></a>Verbinden          
Verbinden Sie Ihren Bot mit Kanälen wie Facebook, Messenger, Kik, Skype, Slack, Microsoft Teams, Telegram, Text/SMS, Twilio, Cortana und Skype für mehr Interaktionen und das Erreichen von mehr Kunden.

[Auswählen der hinzuzufügenden Kanäle](bot-service-manage-channels.md)


## <a name="evaluate"></a>Evaluate 
Verwenden Sie die im Azure-Portal gesammelten Daten, um Möglichkeiten zur Verbesserung der Funktionen und der Leistung Ihres Bots zu ermitteln. Sie können einen Servicelevel und Instrumentierungsdaten wie Datenverkehr, Latenz und Integrationen abrufen. Analytics bietet außerdem Berichterstellung zu Benutzer, Nachricht und Kanaldaten auf Konversationsebene. 

Erfahren Sie, wie [Analysen gesammelt werden](bot-service-manage-analytics.md).

::: moniker-end

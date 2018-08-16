---
title: Szenario für einen Informationsbot | Microsoft-Dokumentation
description: Erkunden Sie das Szenario für einen Informationsbot mit Bot Framework.
author: BrianRandell
ms.author: v-brra
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 43eb8e25e2a17e1d6b1d30e767dd15569fcad78b
ms.sourcegitcommit: 67445b42796d90661afc643c6bb6533e9a662cbc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39574866"
---
# <a name="information-bot-scenario"></a>Szenario für einen Informationsbot

[!INCLUDE [pre-release-label](includes/pre-release-label-v3.md)]

Dieser Informationsbot kann Fragen beantworten, die mithilfe von QnA Maker von Cognitive Services in einer Wissenssammlung oder in häufig gestellten Fragen definiert wurden. Offenere Fragen kann der Bot mithilfe von Azure Search beantworten.

Häufig sind Informationen in strukturierten Datenspeichern wie SQL Server verborgen, die durch eine Suche leicht zugänglich gemacht werden können. Stellen Sie sich vor, Sie schlagen den Bestellstatus eines Kunden mithilfe einfacher Konversationsbefehle nach. Bei Verwendung von QnA Maker von Cognitive Services werden dem Benutzer mehrere gültige Suchoptionen angezeigt, z.B. Nachschlagen eines Kunden, Überprüfen der letzten Bestellung eines Kunden usw. Durch das definierte QnA-Format kann der Benutzer ganz einfach Fragen stellen, die von Azure Search unterstützt werden, und dieser Dienst kann Daten nachschlagen, die in einer SQL-Datenbank gespeichert sind.

![Diagramm des Informationsbots](~/media/scenarios/bot-service-scenario-informational-bot.png)

Hier der logische Ablauf bei einem Informationsbot:

1. Der Angestellte startet den Informationsbot.
2. Azure Active Directory überprüft die Identität des Mitarbeiters.
3. Der Angestellte kann den Bot fragen, welche Arten von Abfragen unterstützt werden.
4. Cognitive Services gibt einen FAQ-Bot zurück, der mit QnA Maker erstellt wurde.
5. Der Angestellte definiert eine gültige Abfrage.
6. Der Bot übermittelt die Abfrage an Azure Search, und Azure Search gibt Informationen über die Anwendungsdaten zurück.
7. Application Insights erfasst die Laufzeittelemetrie, um die Entwicklung der Botleistung und -verwendung zu unterstützen.

## <a name="sample-bot"></a>Beispielbot
Der in C# geschriebene Beispielbot wird in Microsoft Azure ausgeführt und arbeitet mit Daten, die von Azure Search indiziert werden und aus einer SQL-Datenbank-Instanz stammen. Der Bot stellt mithilfe von QnA Maker von Cognitive Services eine Liste von Fragen, die gestellt werden können, sowie Informationen zum Formulieren der Frage (die Antwort) zur Verfügung. Der Benutzer des Bots kann dann eine Abfrage eingeben, mit der über Azure Search nach Daten in einem breiteren oder bestimmten Bereich der Datenbank gesucht wird, der indiziert ist. Das Beispiel stellt eine einfache Datenbank mit Kunden und Bestellinformationen bereit. Application Insights verfolgt die Botnutzung und hilft bei der Überwachung des Bots auf Ausnahmen. Der Bot ist als eine Azure AD-App veröffentlicht, sodass Sie die Personen einschränken können, die Zugriff auf die Informationen haben.

Sie können den Quellcode für diesen Beispielbot unter den [Beispielen für häufige Bot Framework-Szenarios](https://aka.ms/bot/scenarios) herunterladen oder klonen.

## <a name="components-youll-use"></a>Verwendete Komponenten
Der Informationsbot verwendet die folgenden Komponenten:
-   Azure AD zur Authentifizierung
-   Cognitive Services: QnA Maker
-   Azure Search
-   Application Insights

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
Azure Active Directory (Azure AD) ist der mehrinstanzenfähige cloudbasierte Verzeichnis- und Identitätsverwaltungsdienst von Microsoft. Als Botentwickler können Sie sich mit Azure AD vollständig auf das Erstellen Ihres Bots konzentrieren, da Azure AD die schnelle und einfache Integration in eine erstklassige Identitätsverwaltungslösung ermöglicht, die Millionen von Unternehmen auf der ganzen Welt nutzen. Durch das Definieren einer Azure AD-App können Sie steuern, wer auf Ihren Bot und die von ihm freigegebenen Daten zugreifen kann, ohne Ihr eigenes komplexes Authentifizierungs- und Autorisierungssystem zu implementieren.

### <a name="cognitive-services-qna-maker"></a>Cognitive Services: QnA Maker
QnA Maker von Cognitive Services hilft Ihnen bei der Bereitstellung einer Datenquelle für häufig gestellte Fragen, die Benutzer von Ihrem Bot aus abfragen können. Wenn auf große Mengen von Informationen zugegriffen wird, die in verschiedenen Systemen gespeichert sind, kann es hilfreich sein, die Benutzer beim Filtern der Informationsquelle und des Informationssatzes zu unterstützen. Eine einzelne SQL-Datenbank kann über riesige Mengen an Informationen verfügen, wodurch bei Verwendung einer Freiformsuche zu viele Informationen zurückgegeben werden. Wenn Sie zuerst QnA Maker verwenden, können Sie eine Roadmap für die Botbenutzer definieren, damit diese wissen, wie intelligente Fragen zu stellen sind, die dann über Azure Search abgerufen werden können.

### <a name="azure-search"></a>Azure Search
Azure Search ist ein Cloudsuchdienst für Apps, mit dem Ihre Suchindizes schnell einsatzbereit sind. Die Ausführung erfolgt auf Microsoft Azure und kann entsprechend der Nutzungsanforderungen problemlos hoch- und herunterskaliert werden kann. Sie können Suchergebnisse mit Geschäftszielen verbinden und haben dabei umfassende Kontrolle über die Suchrelevanz und können in Ihren Datenbanken verborgene Daten zugänglich machen.

### <a name="application-insights"></a>Application Insights
Mit Application Insights können Sie nützliche Erkenntnisse mit Application Performance Management (APM) und Sofortanalysen sammeln. Sie erhalten ohne großen Konfigurationsaufwand eine umfassende Leistungsüberwachung, leistungsstarke Warnungen und einfach zu bedienende Dashboards, damit die Verfügbarkeit und Leistung Ihres Bots Ihren Erwartungen entspricht. Sie können schnell ermitteln, ob ein Problem besteht, und anschließend eine Ursachenanalyse durchführen, um das Problem zu lokalisieren und zu beheben.

## <a name="next-steps"></a>Nächste Schritte
Als Nächstes lernen Sie das Bot-Szenario für das Internet der Dinge kennen.

> [!div class="nextstepaction"]
> [Bot-Szenario für das Internet der Dinge](bot-service-scenario-internet-things.md)

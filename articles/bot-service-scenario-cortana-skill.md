---
title: Bot für Cortana-Funktionen| Microsoft-Dokumentation
description: Lernen Sie das Szenario für einen Bot für Cortana-Funktionen mit Bot Framework kennen.
author: BrianRandell
ms.author: v-brra
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 2483366c6d325e5c18a77c2a89b63cc0350aef85
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301565"
---
# <a name="cortana-skills-bot-scenario"></a>Szenario für einen Bot für Cortana-Funktionen
Der Bot für Cortana-Funktionen stellt eine Erweiterung für Cortana dar, die es z.B. ermöglicht, ganz einfach mithilfe einer Spracheingabe und Ihrem Kalender einen Inspektionstermin in der Autowerkstatt zu vereinbaren.

Cortana ist Ihre persönliche Assistentin. Mithilfe Ihrer Stimme und dem benutzerdefinierten Bot für Cortana-Funktionen können Sie Cortana darum bitten, mit einer Organisation zu sprechen, also z.B. einer Autowerkstatt, damit Sie leicht einen Termin vereinbaren können. Dieser Dienst kann Ihnen eine Liste mit Dienstleistungen und verfügbaren Terminen anzeigen sowie die Dauer des Termins bestimmen. Außerdem kann Cortana einen Blick in Ihren Kalender werfen, um zu prüfen, ob sich die Termine überschneiden. Wenn dies nicht der Fall ist, vereinbart sie den Termin und trägt ihn in Ihren Kalender ein.

![Diagramm zum Bot für Cortana-Funktionen](~/media/scenarios/bot-service-scenario-cortana-skill.png)

Logischer Ablauf eines Bots für Cortana-Funktionen für eine Autowerkstatt:

1. Der Benutzer greift über seinen Computer oder ein mobiles Gerät auf Cortana zu.
2. Der Benutzer verwendet einen Text- oder Sprachbefehl, um einen Termin in einer Autowerkstatt zu vereinbaren.
3. Da der Bot in Cortana integriert ist, kann er auf den Kalender des Benutzers zugreifen und wendet Logik auf die Anforderung an.
4. Mit diesen Informationen kann der Bot freie Termine der Autowerkstatt ermitteln.
5. In diesem Kontext werden dem Benutzer mögliche Termine angezeigt, aus denen er einen auswählen kann.
6. Application Insights erfasst die Laufzeittelemetrie, um die Entwicklung bei der Botleistung und -verwendung zu unterstützen.

## <a name="sample-bot"></a>Beispielbot
Bei einem Bot für Cortana-Funktionen geht es vor allem um den persönlichen Kontext. Wenn Sie Cortana verwenden, können Sie per Spracheingabe z.B. nach „Bobs mobile Werkstatt“ fragen, damit abhängig von Ihrem Standort ein Mitarbeiter zu Ihnen kommt und sich das Auto ansieht. Cortana hat Zugriff auf Ihre persönlichen Informationen, die Ihrem Bot dabei helfen, den Standort zu bestätigen, an dem sich der Benutzer während des Gesprächs mit dem Bot aufhält.

Sie können den Quellcode für diesen Beispielbot unter [Samples for Common Bot Framework Scenarios (Beispiele für häufig auftretende Bot Framework-Szenarios)](https://aka.ms/bot/scenarios) herunterladen oder klonen.

## <a name="components-youll-use"></a>Komponenten, die Sie verwenden
Der Cortana-Bot verwendet die folgenden Komponenten:
-   Cortana
-   Application Insights

### <a name="cortana"></a>Cortana
Sie können Unterstützung zu Ihrem Bot hinzufügen, indem Sie Cortana-Funktionen erstellen. Verwenden Sie das Cortana-Funktionskit, um neue Features (Cortana-Funktionen) für Cortana erstellen. Mithilfe einer Cortana-Funktion wird Cortana erweitert. Sie erstellen diese Funktionen, um Sie in Ihren Bot zu integrieren, sodass Cortana Aufgaben erledigen kann. Cortana kann (wenn der Benutzer zustimmt) Informationen zum Benutzer zur Laufzeit an eine Funktion übergeben, damit diese ihre Funktionsweise entsprechend anpassen kann. Durch das kontextbezogene Wissen von Cortana ist der Bot sehr nützlich. Einige Arten von Funktionen können, wenn sie einmal aufgerufen wurden, die Schnittstelle von Cortana so manipulieren, dass eine Konversation zwischen der Funktion und dem Endbenutzer entsteht. Wenn Sie Ihre Funktion veröffentlichen, können Benutzer diese sehen und für Cortana für Windows 10 Anniversary Update und höher (Desktopversion und mobile Version), iOS und Android verwenden.

### <a name="application-insights"></a>Application Insights
Mit Application Insights können Sie nützliche Erkenntnisse mit Application Performance Management (APM) und Sofortanalysen sammeln. Sie erhalten eine umfangreiche Überwachung der Leistung, leistungsstarke Warnungen und einfach zu bedienende Dashboards ohne großen Konfigurationsaufwand, damit die Verfügbarkeit und Leistung Ihres Bots Ihren Erwartungen entspricht. Sie können schnell ermitteln, ob ein Problem besteht, und anschließend eine Ursachenanalyse durchführen, um das Problem zu finden und zu beheben.

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie als Nächstes mehr zum Szenario für einen Bot für die Unternehmensproduktivität.

> [!div class="nextstepaction"]
> [Enterprise Productivity bot scenario (Szenario für einen Bot für die Unternehmensproduktivität)](bot-service-scenario-enterprise-productivity.md)

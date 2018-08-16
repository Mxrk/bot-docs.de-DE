---
title: Szenario für einen gewerblichen Bot | Microsoft-Dokumentation
description: Erkunden Sie das Szenario für einen gewerblichen Bot mit Bot Framework.
author: BrianRandell
ms.author: v-brra
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: b809e98ec971abaac98fd33c4fb2c285baca898f
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39303805"
---
# <a name="commerce-bot-scenario"></a>Szenario für einen gewerblichen Bot
Das Szenario für einen [gewerblichen Bot](bot-service-scenario-commerce.md) beschreibt einen Bot, der die herkömmlichen Interaktionen per E-Mail und Telefon ersetzt, die normalerweise zwischen Gästen und dem Portierdienst eines Hotels stattfinden. Der Bot nutzt Cognitive Services, um Kundenanfragen per Text und Sprache mit Kontext, der durch die Integration von Back-End-Diensten erfasst wird, besser zu verarbeiten.

![Diagramm des App-Bots](~/media/scenarios/bot-service-scenario-commerce-bot.png)

Hier der logische Ablauf bei einem gewerblichen Bot, der als Portierdienst für ein Hotel eingesetzt wird:

1. Der Kunde verwendet die mobile Hotel-App.
2. Der Kunde authentifiziert sich mithilfe von Azure AD B2C.
3. Der Kunde fordert Informationen mithilfe des benutzerdefinierten App-Bots an. 
4. Cognitive Services unterstützt das Verarbeiten der Anfrage in natürlicher Sprache.
5. Die Antwort wird vom Kunden überprüft, der die Frage mithilfe natürlicher Konversation verfeinern kann.
6. Sobald der Kunde mit den Ergebnissen zufrieden ist, aktualisiert der App-Bot die Reservierung des Kunden.
7. Application Insights erfasst die Telemetrie der Runtime, um die Entwicklung der Botleistung und -verwendung zu unterstützen.

## <a name="sample-bot"></a>Beispielbot
Das Beispiel eines gewerblichen Bots basiert auf dem Portierservice eines fiktiven Hotels. Der Bot ist in C# geschrieben, und Kunden greifen nach der Authentifizierung mit Azure AD B2C bei einem Hotel über die mobile App für Mitgliederservices der Kette auf den Bot zu. Die Kette speichert Reservierungen in einer SQL-Datenbank. Ein Kunde kann Fragen mit natürlichen Ausdrücken stellen, z.B. „Wie viel kostet es, ein Sonnensegel am Pool für die Dauer meines Aufenthalts zu mieten?“ Der Bot verfügt wiederum über den Kontext, um welches Hotel es sich handelt, und kennt die Dauer des Aufenthalts. Darüber hinaus erleichtert es der LUIS-Dienst (Language Understanding) dem Bot, den Kontext auch einem einfachen Ausdruck wie „Sonnensegel am Pool“ zu entnehmen. Der Bot gibt die Antwort und kann dann anbieten, ein Sonnensegel für den Gast zu buchen, und stellt Auswahlmöglichkeiten für die Anzahl der Tage und den Typ des Sonnensegels bereit. Sobald der Bot über alle erforderlichen Daten verfügt, bucht er die Anfrage. Der Gast kann dieselbe Anfrage auch mit seiner Stimme stellen.

Sie können den Quellcode für diesen Beispielbot unter den [Beispielen für häufige Bot Framework-Szenarios](https://aka.ms/bot/scenarios) herunterladen oder klonen.

## <a name="components-youll-use"></a>Verwendete Komponenten
Der gewerbliche Bot verwendet die folgenden Komponenten:
-   Azure AD für Authentifizierung
-   Cognitive Services: LUIS
-   Application Insights

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
Azure Active Directory (Azure AD) ist der mehrinstanzenfähige cloudbasierte Verzeichnis- und Identitätsverwaltungsdienst von Microsoft. Als Botentwickler können Sie sich dank Azure AD vollständig auf die Erstellung Ihres Bots konzentrieren, da Azure AD die schnelle und einfache Integration in eine erstklassige Identitätsverwaltungslösung ermöglicht, die von Millionen von Unternehmen auf der ganzen Welt verwendet wird. Azure AD unterstützt einen B2C-Connector und somit die Identifizierung von Einzelpersonen anhand externer IDs (beispielsweise Google, Facebook oder Microsoft-Konto). Dank Azure AD müssen Sie sich nicht mehr um die Verwaltung von Anmeldeinformationen des Benutzers kümmern, sondern können sich stattdessen auf Ihre Botlösung konzentrieren, da Sie wissen, dass Sie den Benutzer des Bots mit den richtigen Daten, die von Ihrer Anwendung verfügbar gemacht werden, korrelieren können.

### <a name="cognitive-services-luis"></a>Cognitive Services: LUIS
Als Mitglied der Technologiefamilie Cognitive Services stellt LUIS (Language Understanding) die Leistungsfähigkeit von Machine Learning in Ihren Apps bereit. Derzeit unterstützt LUIS mehrere Sprachen, durch die Ihr Bot verstehen kann, was eine Person wünscht. Beim Integrieren von LUIS geben Sie Absichten an und Definieren die Entitäten, die von Ihrem Bot verstanden werden. Anschließend bringen Sie Ihrem Bot bei, diese Absichten und Entitäten zu verstehen, indem Sie ihn mit Beispieläußerungen trainieren. Sie können die Integration mithilfe von Ausdruckslisten und Funktionen für reguläre Ausdrücke optimieren, damit Ihr Bot so dynamisch wie möglich Ihren speziellen Konversationsanforderungen entspricht.

### <a name="application-insights"></a>Application Insights
Mit Application Insights können Sie über Application Performance Management (APM) und Sofortanalysen nützliche Erkenntnisse gewinnen. Sie erhalten ohne großen Konfigurationsaufwand eine umfassende Leistungsüberwachung, leistungsstarke Warnungen und einfach zu bedienende Dashboards, damit die Verfügbarkeit und Leistung Ihres Bots auch Ihren Erwartungen entspricht. Sie können schnell ermitteln, ob ein Problem besteht, und anschließend eine Ursachenanalyse durchführen, um das Problem zu finden und zu beheben.

## <a name="next-steps"></a>Nächste Schritte
Lernen Sie als Nächstes das Szenario für einen Bot für Cortana-Funktionen kennen.

> [!div class="nextstepaction"]
> [Szenario für einen Bot für Cortana-Funktionen](bot-service-scenario-cortana-skill.md)

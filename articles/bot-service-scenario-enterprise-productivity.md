---
title: Bot für die Unternehmensproduktivität | Microsoft-Dokumentation
description: Hier lernen Sie das Botszenario für Unternehmensproduktivität mit Bot Framework kennen.
author: BrianRandell
ms.author: v-brra
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 4e0bde9d05ed49f6674b2d721e07235b26c5cea4
ms.sourcegitcommit: 67445b42796d90661afc643c6bb6533e9a662cbc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39574756"
---
# <a name="enterprise-productivity-bot-scenario"></a>Bot für die Unternehmensproduktivität

[!INCLUDE [pre-release-label](includes/pre-release-label-v3.md)]

Der Bot für Unternehmen zeigt, wie Sie Ihre Produktivität steigern können, indem Sie einen Bot in Ihren Office 365-Kalender und andere Dienste integrieren.

Er bietet schnellen Zugriff auf Kundeninformationen, ohne dass mehrere Fenster geöffnet werden müssen. Mit einfachen Chatbefehlen kann ein Vertriebsmitarbeiter Kunden suchen und seinen nächsten Termin mit der Graph-API und Office 365 überprüfen. Von dort aus kann er auf kundenspezifische Informationen zugreifen, die in Dynamics CRM gespeichert sind. So lassen sich beispielsweise Fallinformationen abrufen oder ein neuer Fall erstellen.

![Diagramm: Bot für Unternehmen](~/media/scenarios/bot-service-scenario-enterprise-bot.png)

So sieht der Logikfluss eines Bots für Unternehmensproduktivität aus:

1. Ein Mitarbeiter greift auf den Bot für Unternehmensproduktivität zu.
2. Azure Active Directory überprüft die Identität des Mitarbeiters.
3. Der Bot für Unternehmensproduktivität kann über Graph den Office 365-Kalender des Mitarbeiters abfragen.
4. Der Bot hat über die Daten, die er dem Kalender entnimmt, Zugriff auf Fallinformationen in Dynamics CRM.
5. Die Informationen werden an den Mitarbeiter zurückgegeben, der die Daten dann filtern kann, ohne den Bot zu verlassen.
6. Application Insights erfasst die Laufzeittelemetrie, um die Entwicklung der Botleistung und -verwendung zu unterstützen.

Sie können den Quellcode für diesen Beispielbot aus den [Beispielen für häufige Bot Framework-Szenarios](https://aka.ms/bot/scenarios) herunterladen oder klonen.

## <a name="sample-bot"></a>Beispielbot
Da auf Bots über zahlreiche Kanäle zugegriffen werden kann, können Sie sie über ein Unternehmensportal oder unterwegs über Skype nutzen – Sie müssen sich lediglich authentifizieren. Mit der Azure AD-Integration weiß Ihr Bot für Unternehmensproduktivität, dass Sie von Azure AD authentifiziert wurden, wenn Sie Zugriff haben. Dann können Sie den Bot bitten zu überprüfen, wann Ihr nächster Termin bei einem bestimmten Kunden ist. Der Bot ruft diese Information ab, indem er Office 365 über die Graph-API abfragt. Falls in den nächsten sieben Tagen ein Termin ansteht, fragt der Bot das CRM nach aktuellen Fällen für den Kunden ab. Der Bot antwortet entweder, dass keine Fälle gefunden wurden, oder mit der Anzahl von offenen und geschlossenen Fällen. Dann können Sie den Bot bitten, die Fälle nach Typ aufzulisten und in einzelne Fälle zu bohren.

## <a name="components-youll-use"></a>Verwendete Komponenten
Der Bot für Unternehmensproduktivität verwendet die folgenden Komponenten:
-   Azure AD zur Authentifizierung
-   Graph-API für Office 365
-   Dynamics CRM
-   Application Insights

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
Azure Active Directory (Azure AD) ist der mehrinstanzenfähige cloudbasierte Verzeichnis- und Identitätsverwaltungsdienst von Microsoft. Als Botentwickler können Sie sich mit Azure AD vollständig auf das Erstellen Ihres Bots konzentrieren, da Azure AD die schnelle und einfache Integration in eine erstklassige Identitätsverwaltungslösung ermöglicht, die Millionen von Unternehmen auf der ganzen Welt nutzen. Durch das Definieren einer Azure AD-App können Sie steuern, wer auf Ihren Bot und die von ihm freigegebenen Daten zugreifen kann, ohne Ihr eigenes komplexes Authentifizierungs- und Autorisierungssystem zu implementieren.

### <a name="graph-api-to-office-365"></a>Graph-API für Office 365
Microsoft Graph macht mehrere APIs von Office 365 und anderen Microsoft Cloud Services über einen einzigen Endpunkt unter https://graph.microsoft.com verfügbar. Mit Microsoft Graph können Sie und der Bot einfacher Abfragen ausführen. Die API macht Daten aus mehreren Microsoft Cloud Services verfügbar, dazu gehören Exchange Online als Teil von Office 365, Azure Active Directory und SharePoint. Sie können mit der API zwischen Entitäten und Beziehungen navigieren. Sie können die API Ihrer Bots mit dem SDK oder REST-Endpunkten sowie die API Ihrer anderen Apps mit nativer Unterstützung für Android, iOS, Ruby, UWP, Xamarin usw. verwenden.

### <a name="dynamics-crm"></a>Dynamics CRM
Dynamics CRM ist eine Kundeninteraktionsplattform. Mit Bots und CRM-APIs können Sie interaktive Bots erstellen, die auf die in CRM gespeicherten umfangreichen Daten zugreifen können. Die Leistungsfähigkeit von Dynamics CRM steht Ihrem Bot zur Verfügung, um Fälle zu erstellen, den Status zu überprüfen, Suchen in Wissensdatenbanken durchzuführen und vieles mehr.

### <a name="application-insights"></a>Application Insights
Mit Application Insights können Sie nützliche Erkenntnisse mit Application Performance Management (APM) und Sofortanalysen sammeln. Sie erhalten eine umfangreiche Überwachung der Leistung, leistungsstarke Warnungen und einfach zu bedienende Dashboards ohne großen Konfigurationsaufwand, damit die Verfügbarkeit und Leistung Ihres Bots Ihren Erwartungen entspricht. Sie können schnell ermitteln, ob ein Problem besteht, und anschließend eine Ursachenanalyse durchführen, um das Problem zu lokalisieren und zu beheben.

## <a name="next-steps"></a>Nächste Schritte
Als Nächstes lernen Sie das Szenario bei Verwendung eines Informationsbots kennen.

> [!div class="nextstepaction"]
> [Informationsbot](bot-service-scenario-informational.md)

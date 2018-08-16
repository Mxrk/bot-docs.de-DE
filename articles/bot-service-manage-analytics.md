---
title: Botanalysen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihren Bot mit Sammeln und Analysieren von Daten durch Analyse im Bot-Framework verbessern können.
keywords: Botanalyse, Application Insights, Datenverkehr, Latenz, Integrationen, AppInsights
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 503e9b2231b198346f5a7cd767a1e6a866e9e5b3
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301880"
---
# <a name="bot-analytics"></a>Botanalyse
Analytics ist eine Erweiterung von [Application Insights](/azure/application-insights/app-insights-analytics). Application Insights bietet einen **Servicelevel** und Instrumentierungsdaten wie Datenverkehr, Latenz und Integrationen. Analytics bietet auf **Konversationsebene** Berichterstellung an den Benutzer, Nachrichten und Datenkanal.

## <a name="view-analytics-for-a-bot"></a>Anzeigen der Analysen für einen Bot
Um auf Analytics zuzugreifen, öffnen Sie den Bot im Entwicklerportal, und klicken Sie auf **Analytics**.

Zu viele Daten? [Aktivieren und konfigurieren Sie das Erstellen von Stichproben](/azure/application-insights/app-insights-sampling), um Telemetriedatenverkehr und Speicherung zu reduzieren und gleichzeitig eine statistisch korrekte Analyse beizubehalten. 

### <a name="specify-channel"></a>Angeben des Kanals
Wählen Sie aus, welche Kanäle in den folgenden Diagrammen angezeigt werden. Beachten Sie: Wenn ein Bot auf einem Kanal nicht aktiviert ist, erhalten Sie keine Daten über diesen Kanal.

![Auswählen des Kanals](~/media/analytics-channels.png)

* Aktivieren Sie das Kontrollkästchen, um einen Kanal in das Diagramm aufzunehmen.
* Deaktivieren Sie das Kontrollkästchen, um einen Kanal aus dem Diagramm zu entfernen.

### <a name="specify-time-period"></a>Angeben des Zeitraums
Die Analyse ist nur für die letzten 90 Tage verfügbar. Die Datensammlung begann, als Application Insights aktiviert wurde.

![Auswählen des Zeitraums](~/media/analytics-timepick.png)

Klicken Sie auf das Dropdownmenü und dann auf die Zeitspanne, die die Diagramme anzeigen sollen.
Beachten Sie, dass sich bei Änderung des gesamten Zeitrahmens der Zeitschritt (X-Achse) in den Diagrammen entsprechend ändert.

### <a name="grand-totals"></a>Gesamtergebnisse
Die Gesamtzahl aktiver Benutzer und gesendeter und empfangener Nachrichten während des angegebenen Zeitraums.
Striche `--` zeigen an, dass keine Aktivität stattgefunden hat.

### <a name="retention"></a>Aufbewahrung
Die Aufbewahrung verfolgt nach, wie viele Benutzer, die eine Nachricht gesendet haben, später zurückgekehrt sind und eine weitere gesendet haben.
Das Diagramm ist ein 10-Tage-Schiebefenster. Die Ergebnisse sind von der Änderung des Zeitrahmens nicht betroffen.

![Aufbewahrungsdiagramm](~/media/analytics-retention.png)

Beachten Sie, dass das letztmögliche Datum zwei Tage zurück liegt. Ein Benutzer hat vorgestern Nachrichten gesendet und ist dann gestern *zurückgekehrt*.

### <a name="user"></a>Benutzer
Das Benutzerdiagramm verfolgt nach, wie viele Benutzer im angegebenen Zeitrahmen mithilfe welches Kanals auf den Bot zugegriffen haben.

![Benutzerdiagramm](~/media/analytics-users.png)

* Der Prozentsatzdiagramm zeigt, wie viel Prozent der Benutzer welchen Kanal verwendet haben.
* Das Liniendiagramm zeigt an, wie viele Benutzer zu einem bestimmten Zeitpunkt auf den Bot zugegriffen haben.
* Die Legende für das Liniendiagramm gibt an, welche Farbe welchen Kanal darstellt, und enthält die Gesamtzahl der Benutzer während des angegebenen Zeitraums.

### <a name="messages"></a>Meldungen
Das Nachrichtendiagramm verfolgt nach, wie viele Nachrichten im angegebenen Zeitrahmen über welchen Kanal gesendet und empfangen wurden.

![Nachrichtendiagramm](~/media/analytics-messages.png)

* Das Prozentsatzdiagramm zeigt an, wie viel Prozent der Nachrichten über die einzelnen Kanäle übertragen wurden.
* Das Liniendiagramm zeigt an, wie viele Nachrichten im angegebenen Zeitrahmen gesendet und empfangen wurden.
* Die Legende für das Liniendiagramm gibt an, welche Linienfarbe welchen Kanal darstellt, und die Gesamtzahl der im angegebenen Zeitraum über den jeweiligen Kanal gesendeten und empfangenen Nachrichten. 

## <a name="enable-analytics"></a>Aktivieren der Analyse
Analytics ist erst verfügbar, nachdem Application Insights aktiviert und konfiguriert wurde. Application Insights beginnt nach der Aktivierung mit dem Sammeln von Daten. Wenn Application Insights z.B. vor einer Woche für einen sechs Monate alten Bot aktiviert wurde, wurden die Daten der letzten Woche erfasst.
> [!NOTE]
> Analytics erfordert ein Azure-Abonnement und eine Application Insights-[Ressource](/azure/application-insights/app-insights-create-new-resource).
Öffnen Sie für den Zugriff auf Application Insights den Bot im [Bot-Frameworkportal](https://dev.botframework.com/), und klicken Sie auf **Einstellungen**.

1. Erstellen Sie eine Application Insights-[Ressource](/azure/application-insights/app-insights-create-new-resource).
2. Öffnen Sie den Bot im Dashboard. Klicken Sie auf **Einstellungen**, und scrollen Sie nach unten zum Abschnitt **Analyse**.
3. Geben Sie die Informationen zum Herstellen der Verbindung des Bots mit Application Insights ein. Alle Felder sind erforderlich.

![Herstellen der Verbindung mit Application Insights](~/media/analytics-enable.png)

### <a name="appinsights-instrumentation-key"></a>AppInsights-Instrumentierungsschlüssel
Um diesen Wert zu suchen, öffnen Sie Application Insights, und navigieren Sie zu **Konfigurieren** > **Eigenschaften**.

### <a name="appinsights-api-key"></a>AppInsights-API-Schlüssel
Geben Sie einen Azure-App-Insights-API-Schlüssel an. Erfahren Sie, wie Sie [einen neuen API-Schlüssel generieren](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID). Nur die **Leseberechtigung** ist erforderlich.

### <a name="appinsights-application-id"></a>AppInsights-Anwendungs-ID
Um diesen Wert zu suchen, öffnen Sie Application Insights, und navigieren Sie zu **Konfigurieren** > **API-Zugriff**.

Weitere Informationen zum Ermitteln dieser Werte finden Sie unter [Application Insights-Schlüssel](~/bot-service-resources-app-insights-keys.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen
* [Application Insights-Schlüssel](~/bot-service-resources-app-insights-keys.md)
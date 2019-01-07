---
title: Behandeln von HTTP 500-Fehlern bei Bots | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie HTTP 500-Fehler für einen bereitgestellten Bot behandeln.
keywords: Problembehandlung, HTTP 500, Probleme.
author: jonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/20/2018
ms.openlocfilehash: 8ab1cd34f2cc239602db423bccd131d9df39222a
ms.sourcegitcommit: f7a8f05fc05ff4a7212a437d540485bf68831604
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53736007"
---
# <a name="troubleshoot-http-500-errors"></a>Behandeln von HTTP 500-Fehlern

Zum Behandeln von HTTP 500-Fehlern muss zunächst Application Insights aktiviert werden.

Die Beispiele „luis-with-appinsights“ ([C#](https://aka.ms/cs-luis-with-appinsights-sample) / [JS](https://aka.ms/js-luis-with-appinsights-sample)) und „qna-with-appinsights“ ([C#](https://aka.ms/qna-with-appinsights) / [JS](https://aka.ms/js-qna-with-appinsights-sample)) zeigen jeweils Bots, die Azure Application Insights unterstützen. Informationen zum Hinzufügen von Application Insights zu einem bereits vorhandenen Bot finden Sie unter [Conversational Analytics Telemetry](https://aka.ms/botPowerBiTemplate) (Telemetrie für die Konversationsanalyse).

## <a name="enable-application-insights-on-aspnet"></a>Aktivieren von Application Insights in ASP.NET

Eine grundlegende Application Insights-Unterstützung erhalten Sie, indem Sie [Application Insights für Ihre ASP.NET-Website einrichten](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net). Das Bot Framework (ab Version 4.2) liefert zwar zusätzliche Application Insights-Telemetriedaten, diese sind jedoch zum Diagnostizieren von HTTP 500-Fehlern nicht erforderlich.

## <a name="enable-application-insights-on-nodejs"></a>Aktivieren von Application Insights in Node.js

Eine grundlegende Application Insights-Unterstützung erhalten Sie, indem Sie [Ihre Node.js-Dienste und -Apps mit Application Insights überwachen](https://docs.microsoft.com/azure/application-insights/app-insights-nodejs). Das Bot Framework (ab Version 4.2) liefert zwar zusätzliche Application Insights-Telemetriedaten, diese sind jedoch zum Diagnostizieren von HTTP 500-Fehlern nicht erforderlich.

## <a name="query-for-exceptions"></a>Abfragen von Ausnahmen

Bei der Analyse von HTTP-Fehlern mit dem Statuscode 500 beginnen Sie am besten mit Ausnahmen.

Die folgenden Abfragen geben Aufschluss über die neuesten Ausnahmen:

```sql
exceptions
| order by timestamp desc
| project timestamp, operation_Id, appName
```

Wählen Sie aus der ersten Abfrage einige der Vorgangs-IDs aus, und suchen Sie nach weiteren Informationen:

```sql
let my_operation_id = "d298f1385197fd438b520e617d58f4fb";
let union_all = () {
    union
    (traces | where operation_Id == my_operation_id),
    (customEvents | where operation_Id == my_operation_id),
    (requests | where operation_Id == my_operation_id),
    (dependencies | where operation_Id  == my_operation_id),
    (exceptions | where operation_Id == my_operation_id)
};

union_all
    | order by timestamp desc
```

Sollten ausschließlich `exceptions` vorhanden sein, ermitteln Sie anhand der Details, ob sie bestimmten Codezeilen entsprechen. Falls die Ausnahmen alle vom Kanalconnector (`Microsoft.Bot.ChannelConnector`) stammen, lesen Sie unter [Keine Application Insights-Ereignisse](#no-application-insights-events) weiter, um sicherzustellen, dass Application Insights ordnungsgemäß eingerichtet ist und Ihr Code Ereignisse protokolliert.

## <a name="no-application-insights-events"></a>Keine Application Insights-Ereignisse

Falls Sie Fehler vom Typ 500 erhalten und in Application Insights keine weiteren Ereignisse von Ihrem Bot vorhanden sind, führen Sie folgende Schritte aus:

### <a name="ensure-bot-runs-locally"></a>Vergewissern, dass der Bot lokal ausgeführt wird

Vergewissern Sie sich zunächst mithilfe des Emulators, dass Ihr Bot lokal ausgeführt wird.

### <a name="ensure-configuration-files-are-being-copied-net-only"></a>Vergewissern, dass die Konfigurationsdateien kopiert werden (nur .NET)

Vergewissern Sie sich, dass die Konfigurationsdatei vom Typ `.bot` sowie die Datei `appsettings.json` im Rahmen des Bereitstellungsprozesses ordnungsgemäß gepackt werden.

#### <a name="application-assemblies"></a>Application Insights-Assemblys

Vergewissern Sie sich, dass die Application Insights-Assemblys im Rahmen des Bereitstellungsprozesses ordnungsgemäß gepackt werden:

- Microsoft.ApplicationInsights
- Microsoft.ApplicationInsights.TraceListener
- Microsoft.AI.Web
- Microsoft.AI.WebServer
- Microsoft.AI.ServeTelemetryChannel
- Microsoft.AI.PerfCounterCollector
- Microsoft.AI.DependencyCollector
- Microsoft.AI.Agent.Intercept

Vergewissern Sie sich, dass die Konfigurationsdatei vom Typ `.bot` sowie die Datei `appsettings.json` im Rahmen des Bereitstellungsprozesses ordnungsgemäß gepackt werden.

#### <a name="appsettingsjson"></a>appsettings.json

Vergewissern Sie sich in der Datei `appsettings.json`, dass der Instrumentierungsschlüssel festgelegt ist.

## <a name="aspnet-web-apitabdotnetwebapi"></a>[ASP.NET-Web-API](#tab/dotnetwebapi)

```json
{
    "Logging": {
        "IncludeScopes": false,
        "LogLevel": {
            "Default": "Debug",
            "System": "Information",
            "Microsoft": "Information"
        },
        "Console": {
            "IncludeScopes": "true"
        }
    }
}
```

## <a name="aspnet-coretabdotnetcore"></a>[ASP.NET Core](#tab/dotnetcore)

```json
{
    "botFilePath": "mybot.bot",
    "botFileSecret": "<my secret>",
    "ApplicationInsights": {
        "InstrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
}
```

---

### <a name="verify-bot-config-file"></a>Überprüfen der BOT-Konfigurationsdatei

Vergewissern Sie sich, dass Ihre BOT-Datei einen Application Insights-Schlüssel enthält.

```json
    {
        "type": "appInsights",
        "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "subscriptionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "resourceGroup": "my resource group",
        "name": "my appinsights name",
        "serviceName": "my service name",
        "instrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "applicationId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "apiKeys": {},
        "id": ""
    },
```

### <a name="check-logs"></a>Überprüfen der Protokolle

Sowohl ASP.NET als auch Node.js geben Protokolle auf der Serverebene aus, die überprüft werden können.

#### <a name="set-up-a-browser-to-watch-your-logs"></a>Einrichten eines Browsers für die Betrachtung Ihrer Protokolle

1. Öffnen Sie Ihren Bot im [Azure-Portal](http://portal.azure.com/).
1. Öffnen Sie die Seite **mit den App Service-Einstellungen**, um alle Diensteinstellungen anzuzeigen.
1. Öffnen Sie die Seite **Überwachung/Diagnoseprotokolle** für den App-Dienst.
   - Vergewissern Sie sich, dass **Anwendungsprotokollierung (Dateisystem)** aktiviert ist. Klicken Sie auf **Speichern**, falls Sie diese Einstellung ändern müssen.
1. Wechseln Sie zur Seite **Überwachung/Protokollstream**.
   - Wählen Sie **Webserverprotokolle** aus, und achten Sie auf eine Meldung mit dem Hinweis, dass eine Verbindung besteht. Das Ergebnis sollte etwa wie folgt aussehen:

     ```bash
     Connecting...
     2018-11-14T17:24:51  Welcome, you are now connected to log-streaming service.
     ```

     Lassen Sie dieses Fenster geöffnet.

#### <a name="set-up-browser-to-restart-your-bot-service"></a>Einrichten des Browsers zum Neustarten Ihres Botdiensts

1. Öffnen Sie Ihren Bot unter Verwendung eines separaten Browsers im Azure-Portal.
1. Öffnen Sie die Seite **mit den App Service-Einstellungen**, um alle Diensteinstellungen anzuzeigen.
1. Wechseln Sie zur **Übersichtsseite** für den App-Dienst, und klicken Sie auf **Neu starten**.
   - Bestätigen Sie die Nachfrage mit **Ja**.
1. Kehren Sie zum ersten Browserfenster zurück, und sehen Sie sich die Protokolle an.
1. Vergewissern Sie sich, dass neue Protokolle empfangen werden.
   - Sollten keine Aktivitäten stattfinden, stellen Sie Ihren Bot erneut bereit.
   - Wechseln Sie anschließend zur Seite **Anwendungsprotokolle**, und suchen Sie nach Fehlern.

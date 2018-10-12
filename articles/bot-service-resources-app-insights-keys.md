---
title: Application Insights-Schlüssel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Application Insights-Schlüssel abrufen, um einem Bot Telemetriedaten hinzuzufügen.
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 07fb6e9630996a61932da99b0575d43f4604141e
ms.sourcegitcommit: 3cb288cf2f09eaede317e1bc8d6255becf1aec61
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47389429"
---
# <a name="application-insights-keys"></a>Application Insights-Schlüssel

Azure **Application Insights** zeigt Daten über Ihre Anwendung in einer Microsoft Azure-Ressource an. Um Ihrem Bot Telemetriedaten hinzuzufügen, benötigen Sie ein Azure-Abonnement und eine Application Insights-Ressource, die für Ihren Bot erstellt wurde. Von dieser Ressource können Sie die drei Schlüssel zum Konfigurieren Ihres Bots abrufen:

1. Instrumentierungsschlüssel
2. Anwendungs-ID
3. API-Schlüssel

In diesem Thema erfahren Sie, wie Sie diese Application Insights-Schlüssel erstellen.

> [!NOTE]
> Sie hatten während der Boterstellung oder Registrierung die Möglichkeit, **Application Insights** zu *aktivieren* oder zu *deaktivieren*. Wenn Sie *Ein* ausgewählt haben, verfügt Ihr Bot bereits über die erforderlichen Application Insights-Schlüssel. Wenn Sie jedoch *Aus* ausgewählt haben, können Sie diese Schlüssel anhand der Anweisungen in diesem Thema manuell erstellen.

## <a name="instrumentation-key"></a>Instrumentierungsschlüssel

Gehen Sie wie folgt vor, um den Instrumentierungsschlüssel abzurufen:
1. Erstellen Sie im [Azure-Portal](http://portal.azure.com) im Abschnitt „Überwachen“ eine neue **Application Insights**-Ressource (oder verwenden Sie eine vorhandene).
![Screenshot des Portals mit Application Insights](~/media/portal-app-insights-add-new.png)

2. Klicken Sie in der Liste der Application Insights-Ressourcen auf die Application Insights-Ressource, die Sie gerade erstellt haben.

3. Klicken Sie auf **Overview**.

4. Erweitern Sie den Block **Zusammenfassung**, und suchen Sie nach **Instrumentierungsschlüssel**. 
![Screenshot des Portals mit Instrumentierungsschlüssel](~/media/portal-app-insights-instrumentation-key.png)

5. Kopieren Sie den **Instrumentierungsschlüssel**, und fügen Sie ihn in das Feld **Application Insights-Instrumentierungsschlüssel** der Einstellungen für Ihren Bot ein.

## <a name="application-id"></a>Anwendungs-ID

Gehen Sie zum Abrufen der Anwendungs-ID folgendermaßen vor:
1. Klicken Sie in Ihrer Application Insights-Ressource auf **API-Zugriff**.

2. Kopieren Sie die **Anwendungs-ID**, und fügen Sie sie in das Feld **Application Insights-Anwendungs-ID** der Einstellungen für Ihren Bot ein. 
![Screenshot des Portals mit Anwendungs-ID](~/media/portal-app-insights-appid.png)

## <a name="api-key"></a>API-Schlüssel

Gehen Sie wie folgt vor, um den API-Schlüssel abzurufen:
1. Klicken Sie in Ihrer Application Insights-Ressource auf **API-Zugriff**.

2. Klicken Sie auf **API-Schlüssel erstellen**.

3. Geben Sie eine kurze Beschreibung ein, überprüfen Sie die Option **Telementriedaten lesen**, und klicken Sie auf die Schaltfläche **Schlüssel generieren**.
![Screenshot des Portals mit Anwendungs-ID und API-Schlüssel](~/media/portal-app-insights-appid-apikey.png)

   > [!WARNING]
   > Kopieren Sie diesen **API-Schlüssel**, und speichern Sie ihn, da dieser Schlüssel nie wieder angezeigt wird. Wenn Sie diesen Schlüssel verlieren, müssen Sie einen neuen erstellen.

4. Kopieren Sie den API-Schlüssel in das Feld **Application Insights-API-Schlüssel** der Einstellungen für Ihren Bot.

## <a name="additional-resources"></a>Zusätzliche Ressourcen
Weitere Informationen zum Verbinden dieser Felder in den Einstellungen für Ihren Bot finden Sie unter [Aktivieren von Analysen](~/bot-service-manage-analytics.md#enable-analytics).
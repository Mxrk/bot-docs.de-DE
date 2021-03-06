---
title: Verwalten eines Bots | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Bot über das Botdienstportal verwalten.
keywords: Azure-Portal, Botverwaltung, Tests in Webchat, MicrosoftAppID, MicrosoftAppPassword, Anwendungseinstellungen
author: v-ducvo
ms.author: rstand
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: abs
ms.date: 12/13/2017
ms.openlocfilehash: e68db6c1fe9d3d136a8643652df034fb6df2858f
ms.sourcegitcommit: 8b7bdbcbb01054f6aeb80d4a65b29177b30e1c20
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51645600"
---
# <a name="manage-a-bot"></a>Verwalten eines Bots

In diesem Thema wird erläutert, wie Sie einen Bot mit dem Azure-Portal verwalten.

## <a name="bot-settings-overview"></a>Übersicht über die Boteinstellungen

![Übersicht über die Boteinstellungen](~/media/azure-manage-a-bot/overview.png)

Auf dem Blatt **Übersicht** finden Sie detaillierte Informationen über Ihren Bot. Beispielsweise finden Sie dort die **Abonnement-ID**, den **Tarif**, und den **Messagingendpunkt** Ihres Bots.

## <a name="bot-management"></a>Botverwaltung

 Die meisten Optionen für die Verwaltung Ihres Bots finden Sie im Abschnitt **BOTVERWALTUNG**. Unten finden Sie eine Liste der Optionen, mit denen Sie Ihren Bot verwalten können.

![Botverwaltung](~/media/azure-manage-a-bot/bot-management.png)

| Option |  BESCHREIBUNG |
| ---- | ---- |
| **Build** | Die Registerkarte „Build“ bietet Optionen zum Vornehmen von Änderungen an Ihrem Bot. Diese Option ist für **Nur für die Registrierung vorgesehene Bots** nicht verfügbar. |
| **Testen in Webchat** | Nutzen Sie die integrierte Webchatsteuerung, um Ihren Bot schnell zu testen. |
| **Analyse** | Wenn die Analyse für Ihren Bot aktiviert ist, können Sie die Analysedaten anzeigen, die Application Insights für Ihren Bot gesammelt hat. |
| **Channels** | Konfigurieren Sie die Kanäle, die Ihr Bot für die Kommunikation mit Benutzern verwendet. |
| **Einstellungen** | Verwalten Sie verschiedene Botprofileinstellungen wie Anzeigename, Analyse und Messagingendpunkt. |
| **Sprachvorbereitung** | Verwalten Sie die Verbindungen zwischen Ihrer LUIS-App und dem Bing-Spracheingabedienst. |
| **Botdienst – Preise** | Verwalten Sie den Tarif für den Botdienst. |

## <a name="app-service-settings"></a>App-Diensteinstellungen

![App-Diensteinstellungen](~/media/azure-manage-a-bot/app-service-settings.png)

Das Blatt **Anwendungseinstellungen** enthält ausführliche Informationen zu Ihrem Bot, z.B. die Botumgebung, ID, Application Insights-Schlüssel, Microsoft App-ID und das Microsoft App-Kennwort.

### <a name="microsoftappid-and-microsoftapppassword"></a>MicrosoftAppID und MicrosoftAppPassword

Die **MicrosoftAppID** für Ihren Bot finden Sie auf dem Blatt **Einstellungen**. Das **MicrosoftAppPassword** für Ihren Bot wird nur bei der anfänglichen Erstellung des Bots angezeigt.

![MicrosoftAppID und Kennwort](~/media/azure-manage-a-bot/app-settings.png)

> [!NOTE]
> Der Botdienst **Botkanalregistrierung** verfügt über eine *MicrosoftAppID*. Da aber dieser Art von Dienst kein Appdienst zugeordnet ist, gibt es kein Blatt **Anwendungseinstellungen**, auf dem Sie das *MicrosoftAppPassword* finden können. Um das Kennwort zu erhalten, müssen Sie es generieren. Informationen zum Generieren des Kennworts für eine **Botkanalregistrierung**, finden Sie unter [Botkanalregistrierung-Kennwort](bot-service-quickstart-registration.md#bot-channels-registration-password)

## <a name="next-steps"></a>Nächste Schritte
Das Sie nun das Blatt „Botdienst“ im Azure-Portal kennengelernt haben, erfahren Sie jetzt, wie Sie Ihren Bot mit dem Onlinecode-Editor anpassen können.
> [!div class="nextstepaction"]
> [Verwenden des Onlinecode-Editors](bot-service-build-online-code-editor.md)

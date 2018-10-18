---
title: Verbinden eines Bots mit Direct Line | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Verbindung eines Bots mit Direct Line konfigurieren.
keywords: direct line, botkanäle, benutzerdefinierter client, verbinden mit kanälen, konfigurieren
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 10/11/2018
ms.openlocfilehash: d182d5aaf90cf684361b7e97294eff5837732bb4
ms.sourcegitcommit: 54ed5000c67a5b59e23b667547565dd96c7302f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2018
ms.locfileid: "49315096"
---
# <a name="connect-a-bot-to-direct-line"></a>Verbinden eines Bots mit Direct Line

Mit dem Direct Line-Kanal Sie können Ihre eigene Clientanwendung für die Kommunikation mit Ihrem Bot aktivieren. 

## <a name="add-the-direct-line-channel"></a>Hinzufügen des Direct Line-Kanals

Zum Hinzufügen des Direct Line-Kanals öffnen Sie den Bot im [Azure-Portal](https://portal.azure.com/), klicken Sie auf das Blatt **Kanäle** und dann auf **Direct Line**.

![Hinzufügen des Direct Line-Kanals](~/media/bot-service-channel-connect-directline/directline-addchannel.png)

## <a name="add-new-site"></a>Hinzufügen einer neuen Website

Fügen Sie anschließend eine neue Website hinzu, die die Clientanwendung darstellt, die Sie mit Ihrem Bot verbinden möchten. Klicken Sie auf **Neue Website hinzufügen**, geben Sie einen Namen für Ihre Site an, und klicken Sie auf **Fertig**.

![Hinzufügen der Direct Line-Website](~/media/bot-service-channel-connect-directline/directline-addsite.png)

## <a name="manage-secret-keys"></a>Verwalten von geheimen Schlüsseln

Wenn Ihre Website erstellt wird, generiert das Bot-Framework geheime Schlüssel, die Ihre Clientanwendung verwenden kann, um die Direct Line-API-Anforderungen zu [authentifizieren](~/rest-api/bot-framework-rest-direct-line-3-0-authentication.md), die sie zur Kommunikation mit Ihrem Bot ausgibt. Um einen Schlüssel nur als Text anzuzeigen, klicken Sie auf **Anzeigen** für den entsprechenden Schlüssel.

![Anzeigen des Direct Line-Schlüssels](~/media/bot-service-channel-connect-directline/directline-showkey.png)

Kopieren Sie den angezeigten Schlüssel und speichern Sie ihn sicher. Verwenden Sie anschließend den Schlüssel, um die Direct Line-API-Anforderungen zu [authentifizieren](~/rest-api/bot-framework-rest-direct-line-3-0-authentication.md), die Ihr Client zur Kommunikation mit Ihrem Bot ausgibt.
Alternativ können Sie die Direct Line-API verwenden, um den [Schlüssel für ein Token](~/rest-api/bot-framework-rest-direct-line-3-0-authentication.md#generate-token) auszutauschen, mit dem Ihr Client seine nachfolgenden Anforderungen im Rahmen einer einzelnen Konversation authentifizieren kann.

![Kopieren des Direct Line-Schlüssels](~/media/bot-service-channel-connect-directline/directline-copykey.png)

## <a name="configure-settings"></a>Konfigurieren von Einstellungen

Konfigurieren Sie abschließend die Einstellungen für die Website.

- Wählen Sie die Direct Line-Protokollversion, die Ihre Clientanwendung für die Kommunikation mit Ihren Bot verwendet.

> [!TIP]
> Wenn Sie eine neue Verbindung zwischen Ihrer Clientanwendung und dem Bot herstellen, verwenden Sie die Direct Line-API 3.0.

Klicken Sie abschließend auf **Fertig**, um die Websitekonfiguration zu speichern. Sie können diesen Vorgang ab dem Schritt [Neue Website hinzufügen](#add-new-site) für jede Clientanwendung wiederholen, die Sie mit Ihrem Bot verbinden möchten.

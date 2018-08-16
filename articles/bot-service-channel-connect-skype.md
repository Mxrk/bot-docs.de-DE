---
title: Verbinden eines Bots mit Skype | Microsoft-Dokumentation
description: Erfahren Sie, wie ein Bot für den Zugriff über die Skype-Schnittstelle konfiguriert wird.
keywords: skype, botkanäle, skype konfigurieren, veröffentlichen, verbinden mit kanälen
author: v-ducvo
ms.author: RobStand
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 2/1/2018
ms.openlocfilehash: 5dc4063125855113f813b8873b01df84c90e197e
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301856"
---
# <a name="connect-a-bot-to-skype"></a>Verbinden eines Bots mit Skype

Mit Skype bleiben Sie per Sofortnachricht, Telefon und Videoanrufen mit Benutzern verbunden. Erweitern Sie diese Funktion, indem Sie Bots erstellen, die von Benutzern über die Skype-Schnittstelle entdeckt und zur Interaktion verwendet werden können.

Zum Hinzufügen des Skype-Kanals öffnen Sie den Bot im [Azure-Portal](https://portal.azure.com/), klicken Sie auf das Blatt **Kanäle**, und klicken Sie dann auf **Skype**. Dadurch gelangen Sie auf die Einstellungsseite **Skype konfigurieren**. Füllen Sie alle erforderlichen Informationen über Ihren Bot aus, und klicken Sie auf **Speichern**, um die Verbindung mit dem Skype-Kanal herzustellen. Akzeptieren Sie die **Vertragsbedingungen**, und Ihrem Bot wird der Skype-Kanal hinzugefügt.

![Hinzufügen eines Skype-Kanals](~/media/channels/skype-addchannel.png)

## <a name="web-control"></a>Websteuerelement

Um den Bot in Ihre Website einzubetten, können Sie den Code abrufen, indem Sie auf die Schaltfläche **Get embed code** (Einbettungscode abrufen) im Abschnitt **Websteuerelement** klicken.

## <a name="messaging"></a>Nachrichten

In diesem Abschnitt wird konfiguriert, wie Ihr Bot Nachrichten in Skype sendet und empfängt.

## <a name="calling"></a>Anrufen

In diesem Abschnitt wird die Anruf-Funktion von Skype in Ihrem Bot konfiguriert. Sie können angeben, ob **Anrufen** für Ihren Bot aktiviert ist und, falls aktiviert, ob die IVR-Funktionalität oder die Echtzeit-Medien-Funktionalität verwendet werden soll.

## <a name="groups"></a>Gruppen

In diesem Abschnitt wird konfiguriert, ob Ihr Bot einer Gruppe hinzugefügt werden kann, wie er sich in einer Gruppe für Messaging verhält, außerdem können Gruppenvideoanrufe für Anrufbots aktiviert werden.

## <a name="publish"></a>Veröffentlichen

In diesem Abschnitt werden die Veröffentlichungseinstellungen Ihres Bots konfiguriert. Alle Felder mit der Bezeichnung * sind Pflichtfelder.

Bots in der **Vorschauversion** sind auf 100 Kontakte beschränkt. Wenn Sie mehr als 100 Kontakte benötigen, übermitteln Sie Ihren Bot zur Überprüfung. Wenn Sie auf **Zur Überprüfung übermitteln** klicken und dies bestätigt wurde, kann Ihr Bot automatisch in Skype gesucht werden. Wenn Ihre Anforderung nicht genehmigt werden kann, werden Sie darüber benachrichtigt, was Sie ändern müssen, bevor sie genehmigt werden kann.

## <a name="next-steps"></a>Nächste Schritte

* [Skype für Business](bot-service-channel-connect-skypeforbusiness.md)

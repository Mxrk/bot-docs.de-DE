---
title: Verbinden eines Bots mit Skype | Microsoft-Dokumentation
description: Erfahren Sie, wie ein Bot für den Zugriff über die Skype-Schnittstelle konfiguriert wird.
keywords: skype, botkanäle, skype konfigurieren, veröffentlichen, verbinden mit kanälen
author: v-ducvo
ms.author: RobStand
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 10/11/2018
ms.openlocfilehash: 58c8145d359d292dd33972a3dd59af997e7b8393
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49999507"
---
# <a name="connect-a-bot-to-skype"></a>Verbinden eines Bots mit Skype

Mit Skype bleiben Sie per Sofortnachricht, Telefon und Videoanrufen mit Benutzern verbunden. Erweitern Sie diese Funktion, indem Sie Bots erstellen, die von Benutzern über die Skype-Schnittstelle entdeckt und zur Interaktion verwendet werden können.

Zum Hinzufügen des Skype-Kanals öffnen Sie den Bot im [Azure-Portal](https://portal.azure.com/), klicken Sie auf das Blatt **Kanäle**, und klicken Sie dann auf **Skype**.

![Hinzufügen eines Skype-Kanals](~/media/channels/skype-addchannel.png)

Dadurch gelangen Sie auf die Einstellungsseite **Skype konfigurieren**.

![Konfigurieren des Skype-Kanals](~/media/channels/skype_configure.png)

Sie müssen die Einstellungen unter **Websteuerelement**, **Nachrichten**, **Anrufen**, **Gruppen** und **Veröffentlichen** konfigurieren. Wir gehen diese Punkte einzeln durch.

## <a name="web-control"></a>Websteuerelement

Klicken Sie im Abschnitt **Websteuerelement** auf die Schaltfläche **Get embed code** (Einbettungscode abrufen), um den Bot in Ihre Website einzubetten. Sie werden auf die Seite „Skype für Entwickler“ geleitet. Befolgen Sie die angegebene Anleitung, um den Einbettungscode abzurufen.

## <a name="messaging"></a>Nachrichten

In diesem Abschnitt wird konfiguriert, wie Ihr Bot Nachrichten in Skype sendet und empfängt.

## <a name="calling"></a>Anrufen

In diesem Abschnitt wird die Anruf-Funktion von Skype in Ihrem Bot konfiguriert. Sie können angeben, ob **Anrufen** für Ihren Bot aktiviert ist und, falls aktiviert, ob die IVR-Funktionalität oder die Echtzeit-Medien-Funktionalität verwendet werden soll.

## <a name="groups"></a>Gruppen

In diesem Abschnitt wird konfiguriert, ob Ihr Bot einer Gruppe hinzugefügt werden kann, wie er sich in einer Gruppe für Messaging verhält, außerdem können Gruppenvideoanrufe für Anrufbots aktiviert werden.

## <a name="publish"></a>Veröffentlichen

In diesem Abschnitt werden die Veröffentlichungseinstellungen Ihres Bots konfiguriert. Alle Felder mit der Bezeichnung * sind Pflichtfelder.

Bots in der **Vorschauversion** sind auf 100 Kontakte beschränkt. Wenn Sie mehr als 100 Kontakte benötigen, übermitteln Sie Ihren Bot zur Überprüfung. Wenn Sie auf **Zur Überprüfung übermitteln** klicken und dies bestätigt wurde, kann Ihr Bot automatisch in Skype gesucht werden. Wenn Ihre Anforderung nicht genehmigt werden kann, werden Sie darüber benachrichtigt, was Sie ändern müssen, bevor sie genehmigt werden kann.

Klicken Sie nach Abschluss der Konfiguration auf **Speichern**, und akzeptieren Sie die **Vertragsbedingungen**. Der Skype-Kanal wird Ihrem Bot jetzt hinzugefügt.

## <a name="next-steps"></a>Nächste Schritte

* [Skype für Business](bot-service-channel-connect-skypeforbusiness.md)

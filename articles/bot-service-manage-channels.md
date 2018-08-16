---
title: Konfigurieren eines Bots zur Ausführung auf einem oder mehreren Kanälen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Bot mithilfe des Bot Framework-Portals zur Ausführung auf einem oder mehreren Kanälen konfigurieren.
keywords: Botkanäle, Konfigurieren, Cortana, Facebook Messenger, Kik, Slack, Skype, Azure-Portal
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: cb682bf77f801c98d00deffa0fc63249962248cd
ms.sourcegitcommit: dcbc8ad992a3e242a11ebcdf0ee99714d919a877
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/30/2018
ms.locfileid: "39352919"
---
# <a name="connect-a-bot-to-channels"></a>Verbinden eines Bots mit Kanälen

Ein Kanal ist eine Verbindung zwischen dem Bot Framework und Kommunikations-Apps. Sie konfigurieren einen Bot für eine Verbindung mit den Kanälen, auf denen er verfügbar sein soll. Beispielsweise kann ein Bot, der mit dem Skype-Kanal verbunden ist, einer Kontaktliste hinzugefügt werden, und Benutzer können damit in Skype interagieren. 

Die Kanäle umfassen viele beliebte Dienste, wie z.B. [Cortana](bot-service-channel-connect-cortana.md), [Facebook Messenger](bot-service-channel-connect-facebook.md), [Kik](bot-service-channel-connect-kik.md), und [Slack](bot-service-channel-connect-slack.md) sowie verschiedene andere. [Skype](https://dev.skype.com/bots) und Web Chat sind vorkonfiguriert. 

Das Verbinden mit Kanälen kann im [Azure-Portal](https://portal.azure.com) schnell und einfach durchgeführt werden.

## <a name="get-started"></a>Erste Schritte

Bei den meisten Kanälen müssen Sie Informationen für die Kanalkonfiguration bereitstellen, um den Bot auf dem Kanal auszuführen. Die meisten Kanäle erfordern, dass der Bot über ein Konto auf dem Kanal verfügt. Bei anderen Kanälen (z.B. Facebook Messenger) ist es erforderlich, dass der Bot zudem über eine beim Kanal registrierte Anwendung verfügt.

Führen Sie die folgenden Schritte aus, um Ihren Bot für eine Verbindung mit einem Kanal zu konfigurieren:

1. Melden Sie sich beim <a href="https://portal.azure.com" target="_blank">Azure-Portal</a>an.
1. Wählen Sie den Bot aus, den Sie konfigurieren möchten.
3. Klicken Sie auf dem Blatt "Bot Service" unter **Botverwaltung** auf **Kanäle**.
4. Klicken Sie auf das Symbol des Kanals, den Sie Ihrem Bot hinzufügen möchten.

![Verbinden mit Kanälen](~/media/channels/connect-to-channels.png)

Nachdem Sie den Kanal konfiguriert haben, können Benutzer auf diesem Kanal Ihren Bot verwenden.

## <a name="publish-a-bot"></a>Veröffentlichen eines Bots

Der Veröffentlichungsprozess ist für jeden Kanal unterschiedlich.

[!INCLUDE [publishing](~/includes/snippet-publish-to-channel.md)]


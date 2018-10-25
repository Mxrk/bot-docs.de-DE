---
title: Konfigurieren eines Bots zur Ausführung auf einem oder mehreren Kanälen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Bot mithilfe des Bot Framework-Portals zur Ausführung auf einem oder mehreren Kanälen konfigurieren.
keywords: Botkanäle, Konfigurieren, Cortana, Facebook Messenger, Kik, Slack, Skype, Azure-Portal
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 09/22/2018
ms.openlocfilehash: 8c7f10f5f7ce507c367f12b01b4b28ff1cc01c0f
ms.sourcegitcommit: d4afc924b0e1907c4d6f7a6fc5ac1fe521aeef7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2018
ms.locfileid: "47418822"
---
# <a name="connect-a-bot-to-channels"></a>Verbinden eines Bots mit Kanälen

Ein Kanal ist eine Verbindung zwischen dem Bot und Kommunikations-Apps. Sie konfigurieren einen Bot für eine Verbindung mit den Kanälen, auf denen er verfügbar sein soll. Der Bot Framework-Dienst, der über das Azure-Portal konfiguriert wird, verbindet Ihren Bot mit diesen Kanälen und ermöglicht die Kommunikation zwischen Ihrem Bot und dem Benutzer. Sie können Verbindungen mit zahlreichen beliebten Diensten wie z.B. [Cortana](bot-service-channel-connect-cortana.md), [Facebook Messenger](bot-service-channel-connect-facebook.md), [Kik](bot-service-channel-connect-kik.md) und [Slack](bot-service-channel-connect-slack.md) sowie verschiedenen anderen herstellen. [Skype](https://dev.skype.com/bots) und Web Chat sind vorkonfiguriert. Zusätzlich zu den Standardkanälen, die mit dem Bot Connector-Dienst bereitgestellt werden, können Sie Ihren Bot auch mit Ihrer eigenen Clientanwendung verbinden, indem Sie eine Direktverbindung als Ihren Kanal verwenden.

Mit dem Bot Framework-Dienst können Sie Ihren Bot kanalunabhängig entwickeln, indem Sie Nachrichten normalisieren, die der Bot an einen Kanal sendet. Dies schließt die Konvertierung aus dem Botgeneratorschema in das Schema des Kanals ein. Wenn der Kanal jedoch nicht alle Aspekte des Botgeneratorschemas unterstützt, versucht der Dienst, die Nachricht in ein Format zu konvertieren, das der Kanal unterstützt. Wenn der Bot beispielsweise eine Nachricht mit einer Karte mit Aktionsschaltflächen an den SMS-Kanal sendet, kann der Connector die Karte als Bild senden und die Aktionen als Links in den Text der Nachricht aufnehmen.



Bei den meisten Kanälen müssen Sie Informationen für die Kanalkonfiguration bereitstellen, um den Bot auf dem Kanal auszuführen. Die meisten Kanäle erfordern, dass der Bot über ein Konto auf dem Kanal verfügt. Bei anderen Kanälen (z.B. Facebook Messenger) ist es erforderlich, dass der Bot zudem über eine beim Kanal registrierte Anwendung verfügt.

Führen Sie die folgenden Schritte aus, um Ihren Bot für eine Verbindung mit einem Kanal zu konfigurieren:

1. Melden Sie sich beim <a href="https://portal.azure.com" target="_blank">Azure-Portal</a>an.
1. Wählen Sie den Bot aus, den Sie konfigurieren möchten.
3. Klicken Sie auf dem Blatt "Bot Service" unter **Botverwaltung** auf **Kanäle**.
4. Klicken Sie auf das Symbol des Kanals, den Sie Ihrem Bot hinzufügen möchten.

![Verbinden mit Kanälen](./media/channels/connect-to-channels.png)

Nachdem Sie den Kanal konfiguriert haben, können Benutzer auf diesem Kanal Ihren Bot verwenden.

## <a name="publish-a-bot"></a>Veröffentlichen eines Bots

Der Veröffentlichungsprozess ist für jeden Kanal unterschiedlich.

[!INCLUDE [publishing](./includes/snippet-publish-to-channel.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen
Das SDK enthält Beispiele, die Sie zum Erstellen von Bots verwenden können. Die Liste der Beispiele finden Sie im [GitHub](https://github.com/Microsoft/BotBuilder-samples)-Repository.

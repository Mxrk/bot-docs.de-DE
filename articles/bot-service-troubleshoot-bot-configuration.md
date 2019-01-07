---
title: Behandeln von Problemen mit der Botkonfiguration | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Probleme mit der Konfiguration eines bereitgestellten Bots behandeln.
keywords: Problembehandlung, Konfiguration, Webchat, Probleme.
author: jonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/20/2018
ms.openlocfilehash: 18350c7ea6fb7390796567b1754cd88be6b9be61
ms.sourcegitcommit: 561185b9c83f3e082e8b7aba1122b1706e431540
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/26/2018
ms.locfileid: "53785413"
---
# <a name="troubleshoot-bot-configuration-issues"></a>Behandeln von Problemen mit der Botkonfiguration

Der erste Schritt bei der Problembehandlung eines Bots besteht darin, ihn im Webchat zu testen. Dadurch können Sie ermitteln, ob das Problem botspezifisch (Bot funktioniert in keinem Kanal) oder kanalspezifisch (Bot funktioniert nur in einigen Kanälen, in anderen aber nicht) ist.

## <a name="test-in-web-chat"></a>Testen im Webchat

1. Öffnen Sie Ihre Botressource im [Azure-Portal](http://portal.azure.com/).
1. Öffnen Sie den Bereich **Testen im Webchat**.
1. Senden Sie Ihrem Bot eine Nachricht.

![Testen im Webchat](./media/test-in-webchat.png)

Wenn der Bot mit der erwarteten Ausgabe antwortet, fahren Sie mit [Bot funktioniert im Webchat nicht](#bot-does-not-work-in-web-chat) fort. Fahren Sie andernfalls mit [Bot funktioniert im Webchat, aber nicht in anderen Kanälen](#bot-works-in-web-chat-but-not-in-other-channels) fort.

## <a name="bot-does-not-work-in-web-chat"></a>Bot funktioniert im Webchat nicht

Ein nicht funktionierender Bot kann verschiedene Ursachen haben. In den meisten Fällen ist die Botanwendung ausgefallen und kann keine Nachrichten empfangen, oder der Bot empfängt die Nachrichten, kann aber nicht antworten.

So ermitteln Sie, ob der Bot ausgeführt wird:

1. Öffnen Sie den Bereich **Übersicht**.
1. Kopieren Sie den **Messaging-Endpunkt**, und fügen Sie ihn in Ihren Browser ein.

Wenn der Endpunkt den HTTP-Fehler 405 zurückgibt, ist der Bot erreichbar und kann auf Nachrichten antworten. Untersuchen Sie, ob für Ihren Bot ein [Timeout](https://github.com/daveta/analytics/blob/master/troubleshooting_timeout.md) oder ein [HTTP-Fehler vom Typ 5xx](bot-service-troubleshoot-500-errors.md) auftritt.

Wenn der Endpunkt einen Fehler mit dem Hinweis zurückgibt, dass die Website oder Seite nicht erreichbar ist, ist Ihr Bot ausgefallen und muss erneut bereitgestellt werden.

## <a name="bot-works-in-web-chat-but-not-in-other-channels"></a>Bot funktioniert im Webchat, aber nicht in anderen Kanälen

Wenn der Bot im Webchat erwartungsgemäß funktioniert, in einem anderen Kanal aber nicht, kann dies folgende Ursachen haben:

- [Probleme mit der Kanalkonfiguration](#channel-configuration-issues)
- [Kanalspezifisches Verhalten](#channel-specific-behavior)
- [Kanalausfall](#channel-outage)

### <a name="channel-configuration-issues"></a>Probleme mit der Kanalkonfiguration

Unter Umständen wurden die Parameter der Kanalkonfiguration nicht korrekt festgelegt oder von außen geändert. Beispiel: Bei einem Bot ist für eine bestimmte Seite der Facebook-Kanal konfiguriert, und die Seite wurde später gelöscht. Die einfachste Lösung besteht darin, den Kanal zu entfernen und die Kanalkonfiguration erneut auszuführen.

Konfigurationsanleitungen für Kanäle, die von Bot Framework unterstützt werden, finden Sie unter folgenden Links:

- [Cortana](bot-service-channel-connect-cortana.md)
- [DirectLine](bot-service-channel-connect-directline.md)
- [E-Mail](bot-service-channel-connect-email.md)
- [Facebook](bot-service-channel-connect-facebook.md)
- [GroupMe](bot-service-channel-connect-groupme.md)
- [Kik](bot-service-channel-connect-kik.md)
- [Microsoft Teams](https://docs.microsoft.com/microsoftteams/platform/concepts/bots/bots-overview)
- [Skype](bot-service-channel-connect-skype.md)
- [Skype für Business](bot-service-channel-connect-skypeforbusiness.md)
- [Puffer](bot-service-channel-connect-slack.md)
- [Telegram](bot-service-channel-connect-telegram.md)
- [Twilio](bot-service-channel-connect-twilio.md)

### <a name="channel-specific-behavior"></a>Kanalspezifisches Verhalten

Die Implementierung einiger Features kann sich von Kanal zu Kanal unterscheiden. So unterstützen beispielsweise nicht alle Kanäle adaptive Karten. Die meisten Kanäle unterstützen Schaltflächen, diese werden aber kanalspezifisch gerendert. Sollte sich die Funktionsweise einiger Nachrichtentypen in verschiedenen Kanälen unterscheiden, verwenden Sie den [Channel Inspector](https://docs.botframework.com/channel-inspector/channels/Skype).

Im Anschluss finden Sie einige zusätzliche Links, die bei einzelnen Kanälen hilfreich sein können:

- [Add bots to Microsoft Teams apps](https://docs.microsoft.com/microsoftteams/platform/concepts/bots/bots-overview) (Hinzufügen von Bots zu Microsoft Teams-Apps)
- [Facebook: Einführung in die Messenger-Plattform](https://developers.facebook.com/docs/messenger-platform/introduction)
- [Principles of Cortana skills design](https://docs.microsoft.com/cortana/skills/design-principles) (Prinzipien der Gestaltung von Cortana-Funktionen)
- [Skype for Developers](https://dev.skype.com/bots) (Skype für Entwickler)
- [Slack: Enabling interactions with bots](https://api.slack.com/bot-users) (Slack: Ermöglichen von Interaktionen mit Bots)

### <a name="channel-outage"></a>Kanalausfall

Bei einigen Kanälen kann es bisweilen zu Dienstunterbrechungen kommen. Solche Ausfälle dauern in der Regel nicht lang. Sollten Sie jedoch einen Ausfall vermuten, konsultieren Sie eine Kanalwebsite oder soziale Medien.

Sie können auch einen Testbot (etwa einen einfachen Echobot) erstellen und einen Kanal hinzufügen, um zu ermitteln, ob ein Kanal ausgefallen ist. Wenn der Testbot mit einigen Kanälen funktioniert, mit anderen aber nicht, deutet dies darauf hin, dass das Problem nicht auf Ihren Produktionsbot zurückzuführen ist.

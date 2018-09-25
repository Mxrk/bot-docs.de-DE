---
title: Verbinden eines Bots mit Slack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Verbindung eines Bots mit Slack konfigurieren.
keywords: connect a bot, bot channel, Slack bot, Slack messaging app
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 56fe0af4d34e6e0aa4bc420112c541a410aa1301
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301893"
---
# <a name="connect-a-bot-to-slack"></a>Verbinden eines Bots mit Slack

Sie können Ihren Bot so konfigurieren, dass er mit Personen kommuniziert, die die Slack-Messaging-App verwenden.

## <a name="create-a-slack-application-for-your-bot"></a>Erstellen einer Slack-Anwendung für Ihren Bot

Melden Sie sich bei Slack an, und [erstellen Sie eine Slack-Anwendung](https://api.slack.com/applications/new).

![Einrichten des Bots](~/media/channels/slack-NewApp.png)

## <a name="create-an-app-and-assign-a-development-slack-team"></a>Erstellen einer App und Zuweisen eines Slack-Entwicklungsteams

Geben Sie einen App-Namen ein, und wählen Sie ein Slack-Entwicklungsteam aus. Wenn Sie noch kein Mitglied eines Slack-Entwicklungsteams sind, [erstellen Sie eines, oder treten Sie einem bei](https://slack.com/).

![App erstellen](~/media/channels/slack-CreateApp.png)

Klicken Sie auf **Create app** (App erstellen). Slack erstellt Ihre App und generiert eine Client-ID sowie das Clientgeheimnis.

## <a name="add-a-new-redirect-url"></a>Hinzufügen einer neuen Umleitungs-URL

Fügen Sie als Nächstes eine neue Umleitungs-URL hinzu.

1. Wählen Sie die Registerkarte **OAuth & Permissions** (OAuth & Berechtigungen) aus.
2. Klicken Sie auf **Add a new Redirect URL** (Eine neue Umleitungs-URL hinzufügen).
3. Geben Sie https://slack.botframework.com ein.
4. Klicken Sie auf **Hinzufügen**.
5. Klicken Sie auf **Save URLs** (URLs speichern).

![Hinzufügen einer Umleitungs-URL](~/media/channels/slack-RedirectURL.png)

## <a name="create-a-slack-bot-user"></a>Erstellen eines Slack-Bot-Benutzers

Durch Hinzufügen eines Bot-Benutzers können Sie einen Benutzernamen für Ihren Bot zuweisen und auswählen, ob dieser immer als online angezeigt werden soll.

1. Klicken Sie auf die Registerkarte **Bot Users** (Bot-Benutzer).
2. Klicken Sie auf **Add a Bot User** (Bot-Benutzer hinzufügen).

![Erstellen eines Bots](~/media/channels/slack-CreateBot.png)

Klicken Sie auf **Add Bot User** (Bot-Benutzer hinzufügen), um Ihre Einstellungen zu überprüfen, wählen Sie für **Always Show My Bot as Online** (Meinen Bot immer als online anzeigen) **On** (Ein) aus, und klicken Sie dann auf **Save Changes** (Änderungen speichern).

![Erstellen eines Bots](~/media/channels/slack-CreateApp-AddBotUser.png)

## <a name="subscribe-to-bot-events"></a>Abonnieren von Bot-Ereignissen

Abonnieren Sie anhand der folgenden Schritte sechs spezielle Bot-Ereignisse. Durch das Abonnieren von Bot-Ereignissen wird Ihre App über Benutzeraktivitäten unter der URL, die Sie angeben, benachrichtigt.

> [!TIP]
> Ihr Bot-Handle ist eine Eigenschaft Ihres Bots. Um einen Bot-Handle zu finden, rufen Sie [https://dev.botframework.com/bots](https://dev.botframework.com/bots) auf, wählen Sie einen Bot, und klicken Sie auf **SETTINGS** (EINSTELLUNGEN).

1. Klicken Sie auf die Registerkarte **Event Subscriptions** (Ereignisabonnements).
2. Wählen Sie für **Enable Events** (Ereignisse aktivieren) **On** (Ein) aus.
3. Geben Sie unter **Request URL** (Anforderungs-URL) die folgende URL ein, ersetzen Sie `{YourBotHandle}` jedoch durch Ihren Bot-Handle.
        `https://slack.botframework.com/api/Events/{YourBotHandle}`
4. Klicken Sie unter **Subscribe to Bot Events** (Bot-Ereignisse abonnieren) auf **Add Bot User Event** (Bot-Benutzerereignis hinzufügen).
5. Klicken Sie in der Liste der Ereignisse auf **Add Bot User Event** (Bot-Benutzerereignis hinzufügen), und wählen Sie die folgenden sechs Ereignistypen aus:
    * `member_joined_channel`
    * `member_left_channel`
    * `message.channels`
    * `message.groups`
    * `message.im`
    * `message.mpim`
6. Klicken Sie auf **Änderungen speichern**.

![Abonnieren von Ereignissen](~/media/channels/slack-EnableEvents.png)

## <a name="add-and-configure-interactive-messages-optional"></a>Hinzufügen und Konfigurieren von interaktiven Nachrichten (optional)

Wenn Ihr Bot Slack-spezifische Funktionen wie Schaltflächen verwendet, gehen Sie wie folgt vor:

1. Klicken Sie auf die Registerkarte **Interactive Components** (Interaktive Komponenten) und dann auf **Enable Interactive Components** (Interaktive Komponenten aktivieren).
2. Geben Sie `https://slack.botframework.com/api/Actions` als **Request URL** (Anforderungs-URL) ein.
3. Klicken Sie auf die Schaltfläche **Enable Interactive Messages** (Interaktive Nachrichten aktivieren) und dann auf die Schaltfläche **Save changes** (Änderungen speichern).

![Aktivieren von Nachrichten](~/media/channels/slack-MessageURL.png)

## <a name="gather-credentials"></a>Erfassen von Anmeldeinformationen

Klicken Sie auf die Registerkarte **Basic Information** (Grundlegende Informationen), und scrollen Sie zum Abschnitt **App Credentials** (Anmeldeinformationen für die App).
Die Client-ID, das Clientgeheimnis und das Überprüfungstoken für die Konfiguration Ihres Slack-Bots werden angezeigt.

![Erfassen von Anmeldeinformationen](~/media/channels/slack-AppCredentials.png)

## <a name="submit-credentials"></a>Senden von Anmeldeinformationen

Rufen Sie in einem separaten Browserfenster die Bot Framework-Website unter `https://dev.botframework.com/` auf.

1. Klicken Sie auf **My bots** (Meine Bots), und wählen Sie den Bot, für den eine Verbindung mit Slack hergestellt werden soll.
2. Klicken Sie im Abschnitt **Add a channel** (Kanal hinzufügen) auf das Slack-Symbol.
3. Fügen Sie auf der Slack-Website im Abschnitt **Enter your Slack credentials** (Slack-Anmeldeinformationen eingeben) die Anmeldeinformationen für die App in die entsprechenden Felder ein.
4. Die Angabe von **Landing Page URL** (URL der Landing Page) ist optional. Sie können diese auslassen oder ändern.
5. Klicken Sie auf **Speichern**.

![Senden von Anmeldeinformationen](~/media/channels/slack-SubmitCredentials.png)

Befolgen Sie die Anweisungen, um Ihr Slack-Entwicklungsteam für den Zugriff auf Ihre Slack-App zu autorisieren.

## <a name="enable-the-bot"></a>Aktivieren des Bots

Vergewissern Sie sich, dass auf der Seite „Configure Slack“ (Slack konfigurieren) der Schieberegler für die Schaltfläche „Save“ (Speichern) auf **Enabled** (Aktiviert) festgelegt ist.
Ihr Bot ist für die Kommunikation mit Benutzern in Slack konfiguriert.

## <a name="create-an-add-to-slack-button"></a>Erstellen einer Schaltfläche namens „Add to Slack“ (Zu Slack hinzufügen)

Im Abschnitt *Add the Slack button* (Slack-Schaltfläche hinzufügen) [auf dieser Seite](https://api.slack.com/docs/slack-button) stellt Slack einen HTML-Code bereit, um Slack-Benutzern die Suche nach Ihrem Bot zu vereinfachen.
Um diesen HTML-Code mit Ihrem Bot zu verwenden, ersetzen Sie den href-Wert (beginnt mit `https://`) durch die URL in den Slack-Kanaleinstellungen Ihres Bots.
Um die Ersatz-URL abzurufen, gehen Sie wie folgt vor:

1. Klicken Sie unter [https://dev.botframework.com/bots](https://dev.botframework.com/bots) auf Ihren Bot.
2. Klicken Sie im Eintrag mit dem Namen **Slack** mit der rechten Maustaste auf **CHANNELS** (KANÄLE), und klicken Sie dann auf **Copy link** (Link kopieren). Diese URL ist jetzt in der Zwischenablage gespeichert.
3. Fügen Sie diese URL aus der Zwischenablage in den HTML-Code ein, der für die Slack-Schaltfläche bereitgestellt wird. Diese URL ersetzt den href-Wert, der von Slack für diesen Bot bereitgestellt wird.

Autorisierte Benutzer können auf die Schaltfläche **Add to Slack** (Zu Slack hinzufügen) klicken, die von diesem geänderten HTML-Code bereitgestellt wird, um eine Verbindung mit Ihrem Bot in Slack herzustellen.
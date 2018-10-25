---
title: Verbinden eines Bots mit Facebook Messenger | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Verbindung eines Bots mit Facebook Messenger konfigurieren.
keywords: Facebook Messenger, Botkanal, Facebook-App, App-ID, App-Geheimnis, Facebook-Bot, Anmeldeinformationen
author: RobStand
ms.author: RobStand
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 10/12/2018
ms.openlocfilehash: 0932372c5b2bcf574d244cd60d46ef579acbd106
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "50000157"
---
# <a name="connect-a-bot-to-facebook"></a>Verbinden eines Bots mit Facebook

Ihr Bot kann sowohl mit Facebook Messenger als auch mit Facebook Workplace verbunden werden, um mit Benutzern auf beiden Plattformen kommunizieren zu können. Im folgenden Tutorial wird veranschaulicht, wie Sie einen Bot Schritt für Schritt mit diesen beiden Kanälen verbinden.

> [!NOTE]
> Die Facebook-Benutzeroberfläche kann je nach der von Ihnen verwendeten Version variieren.

## <a name="connect-a-bot-to-facebook-messenger"></a>Verbinden eines Bots mit Facebook Messenger

Weitere Informationen zum Entwickeln für Facebook Messenger finden Sie in der [Dokumentation zur Messenger-Plattform](https://developers.facebook.com/docs/messenger-platform). Sie können auch die [Pre-Launch Guidelines](https://developers.facebook.com/docs/messenger-platform/product-overview/launch#app_public) (Hinweise vor dem Start), [Quick Start](https://developers.facebook.com/docs/messenger-platform/guides/quick-start) (Schnellstart) und das [setup guide](https://developers.facebook.com/docs/messenger-platform/guides/setup) (Installationshandbuch) von Facebook lesen.

Um einen Bot für die Kommunikation über Facebook Messenger zu konfigurieren, aktivieren Sie Facebook Messenger auf einer Facebook-Seite, und verbinden Sie dann den Bot mit der App.

### <a name="copy-the-page-id"></a>Kopieren der Seiten-ID

Auf den Bot wird über eine Facebook-Seite zugegriffen. [Erstellen Sie eine neue Facebook-Seite](https://www.facebook.com/bookmarks/pages), oder navigieren Sie zu einer vorhandenen Seite.

* Öffnen Sie die Seite **Info** der Facebook-Seite, und kopieren und speichern Sie die **Seiten-ID**.

### <a name="create-a-facebook-app"></a>Erstellen einer Facebook-App

[Erstellen Sie eine neue Facebook-App](https://developers.facebook.com/quickstarts/?platform=web) auf der Seite, und generieren Sie eine App-ID und ein App-Geheimnis für diese.

![Erstellen einer App-ID](~/media/channels/FB-CreateAppId.png)

* Kopieren und speichern Sie die **App-ID** und das **App-Geheimnis**.

![Speichern von App-ID und -Geheimnis](~/media/channels/FB-get-appid.png)

Legen Sie den Schieberegler „Allow API Access to App Settings“ (API-Zugriff auf App-Einstellungen zulassen) auf „Yes“ (Ja) fest.

![App-Einstellungen](~/media/bot-service-channel-connect-facebook/api_settings.png)

### <a name="enable-messenger"></a>Aktivieren von Messenger

Aktivieren Sie Facebook Messenger in der neuen Facebook-App.

![Aktivieren von Messenger](~/media/channels/FB-AddMessaging1.png)

### <a name="generate-a-page-access-token"></a>Generieren eines Seitenzugriffstokens

Wählen Sie im Abschnitt „Messenger“ im Bereich **Token Generation** (Tokenerstellung) die Zielseite aus. Es wird ein Seitenzugriffstoken generiert.

* Kopieren und speichern Sie das **Seitenzugriffstoken**.

![Generieren eines Tokens](~/media/channels/FB-generateToken.png)

### <a name="enable-webhooks"></a>Aktivieren von Webhooks

Klicken Sie auf **Set up Webhooks** (Webhooks einrichten), um Messagingereignisse von Facebook Messenger an den Bot weiterzuleiten.

![Aktivieren von Webhooks](~/media/channels/FB-webhook.png)

### <a name="provide-webhook-callback-url-and-verify-token"></a>Angeben der Webhook-Rückruf-URL und Überprüfen des Tokens

Öffnen Sie im [Azure-Portal](https://portal.azure.com/) den Bot, klicken Sie auf die Registerkarte **Kanäle** und dann auf **Facebook Messenger**.

* Kopieren Sie die Werte in **Rückruf-URL** und **Token überprüfen** aus dem Portal.

![Kopieren von Werten](~/media/channels/fb-callbackVerify.png)

1. Wechseln Sie zurück zu Facebook Messenger, und fügen Sie die Werte aus **Rückruf-URL** und **Token überprüfen** ein.

2. Wählen Sie unter **Subscription Fields** (Abonnementfelder) die Optionen *message\_deliveries*, *messages*, *messaging\_options* und *messaging\_postbacks* aus.

3. Klicken Sie auf **Überprüfen und speichern**.

![Konfigurieren von Webhooks](~/media/channels/FB-webhookConfig.png)

4. Abonnieren Sie den Webhook der Facebook-Seite.

![Abonnieren von Webhooks](~/media/bot-service-channel-connect-facebook/subscribe-webhook.png)


### <a name="provide-facebook-credentials"></a>Angeben von Facebook-Anmeldeinformationen

Fügen Sie im Azure-Portal die Werte für **Facebook-App-ID**, **Facebook-App-Geheimnis**, **Seiten-ID** und **Seitenzugriffstoken** ein, die Sie zuvor in Facebook Messenger kopiert haben. Sie können den gleichen Bot auf mehreren Facebook-Seiten verwenden, indem Sie zusätzliche Seiten-IDs und Zugriffstoken hinzufügen.

![Eingeben von Anmeldeinformationen](~/media/channels/fb-credentials2.png)

### <a name="submit-for-review"></a>Übermitteln zur Überprüfung

Facebook erfordert eine Datenschutzrichtlinien-URL und eine Nutzungsbedingungen-URL auf der Seite mit den Grundeinstellungen der App. Die Seite mit dem [Verhaltenskodex](https://investor.fb.com/corporate-governance/code-of-conduct/default.aspx) enthält Links zu Ressourcen von Drittanbietern, die Ihnen beim Erstellen einer Datenschutzrichtlinie helfen. Die Seite [Nutzungsbedingungen](https://www.facebook.com/terms.php) enthält Beispielbedingungen zur Unterstützung beim Erstellen eines geeigneten Dokuments mit Nutzungsbedingungen.

Nachdem der Bot fertiggestellt wurde, führt Facebook einen eigenen [Überprüfungsprozess](https://developers.facebook.com/docs/messenger-platform/app-review) für Apps durch, die für Messenger veröffentlicht werden. Der Bot wird getestet, um sicherzustellen, dass er mit den [Plattformrichtlinien](https://developers.facebook.com/docs/messenger-platform/policy-overview) von Facebook konform ist.

### <a name="make-the-app-public-and-publish-the-page"></a>Veröffentlichen der App und der Seite

> [!NOTE]
> Bis zu ihrer Veröffentlichung befindet sich eine App im [Entwicklungsmodus](https://developers.facebook.com/docs/apps/managing-development-cycle). Die Funktionen von Plug-Ins und APIs sind nur für Administratoren, Entwickler und Tester aktiv.

Nach der erfolgreichen Überprüfung legen Sie die App auf dem App-Dashboard unter „App-Prüfung“ als öffentlich fest.
Stellen Sie sicher, dass die Facebook-Seite, der dieser Bot zugeordnet ist, öffentlich ist. Der Status wird in den Seiteneinstellungen angezeigt.

## <a name="connect-a-bot-to-facebook-workplace"></a>Verbinden eines Bots mit Facebook Workplace

Im [Workplace Help Center](https://workplace.facebook.com/help/work/) finden Sie weitere Informationen zu Facebook Workplace, und die [Entwicklerdokumentation für Workplace](https://developers.facebook.com/docs/workplace) enthält Richtlinien zur Entwicklung für Facebook Workplace.

Erstellen Sie eine benutzerdefinierte Integration, und verbinden Sie Ihren Bot damit, um den Bot für die Kommunikation über Facebook Workplace zu konfigurieren.

### <a name="create-a-facebook-workplace-premium-account"></a>Erstellen eines Premium-Kontos für Facebook Workplace

Befolgen Sie [diese Anleitung](https://www.facebook.com/workplace), um ein Premium-Konto für Facebook Workplace zu erstellen und sich selbst als Systemadministrator festzulegen. Beachten Sie, dass nur der Systemadministrator einer Workplace-Instanz benutzerdefinierte Integrationen erstellen kann.

### <a name="create-a-custom-integration"></a>Erstellen einer benutzerdefinierten Integration

Wenn Sie eine benutzerdefinierte Integration erstellen, werden eine App mit definierten Berechtigungen und eine Seite vom Typ „Bot“ erstellt, die nur innerhalb Ihrer Workplace-Community sichtbar sind.

Erstellen Sie eine [benutzerdefinierte Integration](https://developers.facebook.com/docs/workplace/custom-integrations-new) für Ihre Workplace-Instanz, indem Sie die folgenden Schritte ausführen:

- Öffnen Sie im **Administratorbereich** die Registerkarte **Integrationen**.
- Klicken Sie auf die Schaltfläche **Create your own custom App** (Eigene benutzerdefinierte App erstellen).

![Workplace-Integration](~/media/channels/fb-integration.png)

- Wählen Sie einen Anzeigenamen und ein Profilbild für die App aus. Diese Informationen werden mit der Seite vom Typ „Bot“ geteilt.
- Legen Sie den Schieberegler **Allow API Access to App Settings** (API-Zugriff auf App-Einstellungen zulassen) auf „Yes“ (Ja) fest.
- Kopieren Sie die angezeigte App-ID, das App-Geheimnis und das App-Token, und speichern Sie diese Angaben an einem sicheren Ort.

![Workplace-Schlüssel](~/media/channels/fb-keys.png)

Sie haben die Erstellung einer benutzerdefinierten Integration jetzt abgeschlossen. Die Seite vom Typ „Bot“ finden Sie in Ihrer Workplace-Community (wie unten dargestellt).

![Workplace-Seite](~/media/channels/fb-page.png)

### <a name="provide-facebook-credentials"></a>Angeben von Facebook-Anmeldeinformationen

Fügen Sie im Azure-Portal die Werte für **Facebook-App-ID**, **Facebook-App-Geheimnis** und **Seitenzugriffstoken** ein, die Sie zuvor in Facebook Workplace kopiert haben. Verwenden Sie anstelle einer herkömmlichen pageID die Zahlen, die nach dem Namen der Integration auf der Seite **Info** angezeigt werden. Ähnlich wie beim Verbinden eines Bots mit Facebook Messenger können die Webhooks mit den in Azure angezeigten Anmeldeinformationen verbunden werden.

### <a name="submit-for-review"></a>Übermitteln zur Überprüfung
Ausführlichere Informationen hierzu finden Sie im Abschnitt **Verbinden eines Bots mit Facebook Messenger** und in der [Entwicklerdokumentation zu Workplace](https://developers.facebook.com/docs/workplace).

### <a name="make-the-app-public-and-publish-the-page"></a>Veröffentlichen der App und der Seite
Ausführliche Informationen finden Sie im Abschnitt **Verbinden eines Bots mit Facebook Messenger**.

## <a name="sample-code"></a>Beispielcode

Sie können zu Referenzzwecken auch den Beispielbot <a href="https://aka.ms/facebook-events" target="_blank">Facebook-events</a> verwenden, um die Botkommunikation mit Facebook Messenger zu untersuchen.

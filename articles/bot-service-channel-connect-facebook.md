---
title: Verbinden eines Bots mit Facebook Messenger | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Verbindung eines Bots mit Facebook Messenger konfigurieren.
keywords: Facebook Messenger, Botkanal, Facebook-App, App-ID, App-Geheimnis, Facebook-Bot, Anmeldeinformationen
author: RobStand
ms.author: RobStand
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 0a9ad7d51234b417d5d0f27dbcffe4ce839ba94a
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301207"
---
# <a name="connect-a-bot-to-facebook-messenger"></a>Verbinden eines Bots mit Facebook Messenger

Weitere Informationen zum Entwickeln für Facebook Messenger finden Sie in der [Dokumentation zur Messenger-Plattform](https://developers.facebook.com/docs/messenger-platform). Sie können auch die [Pre-Launch Guidelines](https://developers.facebook.com/docs/messenger-platform/product-overview/launch#app_public) (Hinweise vor dem Start), [Quick Start](https://developers.facebook.com/docs/messenger-platform/guides/quick-start) (Schnellstart) und das [setup guide](https://developers.facebook.com/docs/messenger-platform/guides/setup) (Installationshandbuch) von Facebook lesen.

Um einen Bot für die Kommunikation über Facebook Messenger zu konfigurieren, aktivieren Sie Facebook Messenger auf einer Facebook-Seite, und verbinden Sie dann den Bot mit der App.

[!INCLUDE [Channel Inspector intro](~/includes/snippet-channel-inspector.md)]

> [!NOTE]
> Die Facebook-Benutzeroberfläche kann je nach der von Ihnen verwendeten Version variieren.

## <a name="copy-the-page-id"></a>Kopieren der Seiten-ID

Auf den Bot wird über eine Facebook-Seite zugegriffen. [Erstellen Sie eine neue Facebook-Seite](https://www.facebook.com/bookmarks/pages), oder navigieren Sie zu einer vorhandenen Seite.

* Öffnen Sie die Seite **Info** der Facebook-Seite, und kopieren und speichern Sie die **Seiten-ID**.

## <a name="create-a-facebook-app"></a>Erstellen einer Facebook-App

[Erstellen Sie eine neue Facebook-App](https://developers.facebook.com/quickstarts/?platform=web) auf der Seite, und generieren Sie eine App-ID und ein App-Geheimnis für diese.

![Erstellen einer App-ID](~/media/channels/FB-CreateAppId.png)

* Kopieren und speichern Sie die **App-ID** und das **App-Geheimnis**.

![Speichern von App-ID und -Geheimnis](~/media/channels/FB-get-appid.png)

Legen Sie den Schieberegler „Allow API Access to App Settings“ (API-Zugriff auf App-Einstellungen zulassen) auf „Yes“ (Ja) fest.

![App-Einstellungen](~/media/bot-service-channel-connect-facebook/api_settings.png)

## <a name="enable-messenger"></a>Aktivieren von Messenger


Aktivieren Sie Facebook Messenger in der neuen Facebook-App.

* Klicken Sie auf der Seite **Product Setup** (Produkteinrichtung) der App auf **Get Started** (Erste Schritte), und klicken Sie dann erneut auf **Get Started** (Erste Schritte).


![Aktivieren von Messenger](~/media/channels/FB-AddMessaging1.png)

## <a name="generate-a-page-access-token"></a>Generieren eines Seitenzugriffstokens

Wählen Sie im Abschnitt „Messenger“ im Bereich **Token Generation** (Tokenerstellung) die Zielseite aus. Es wird ein Seitenzugriffstoken generiert.

* Kopieren und speichern Sie das **Seitenzugriffstoken**.

![Generieren eines Tokens](~/media/channels/FB-generateToken.png)

## <a name="enable-webhooks"></a>Aktivieren von Webhooks

Klicken Sie auf **Set up Webhooks** (Webhooks einrichten), um Messagingereignisse von Facebook Messenger an den Bot weiterzuleiten.

![Aktivieren von Webhooks](~/media/channels/FB-webhook.png)

## <a name="provide-webhook-callback-url-and-verify-token"></a>Angeben der Webhook-Rückruf-URL und Überprüfen des Tokens

Navigieren Sie zurück zum [Bot Framework-Portal](https://dev.botframework.com/). Öffnen Sie den Bot, und klicken Sie auf die Registerkarte **Kanäle** und dann auf **Facebook Messenger**.

* Kopieren Sie die Werte in **Rückruf-URL** und **Token überprüfen** aus dem Portal.

![Kopieren von Werten](~/media/channels/fb-callbackVerify.png)

1. Wechseln Sie zurück zu Facebook Messenger, und fügen Sie die Werte aus **Rückruf-URL** und **Token überprüfen** ein.

2. Wählen Sie unter **Subscription Fields** (Abonnementfelder) die Optionen *message\_deliveries*, *messages*, *messaging\_options* und *messaging\_postbacks* aus.

3. Klicken Sie auf **Überprüfen und speichern**.

![Konfigurieren von Webhooks](~/media/channels/FB-webhookConfig.png)

4. Abonnieren Sie den Webhook der Facebook-Seite.

![Abonnieren von Webhooks](~/media/bot-service-channel-connect-facebook/subscribe-webhook.png)


## <a name="provide-facebook-credentials"></a>Angeben von Facebook-Anmeldeinformationen

Fügen Sie im Bot Framework-Portal die Werte für **Seiten-ID**, **App-ID**, **App-Geheimnis** und **Seitenzugriffstoken** ein, die Sie zuvor in Facebook Messenger kopiert haben.

![Eingeben von Anmeldeinformationen](~/media/channels/fb-credentials2.png)

## <a name="submit-for-review"></a>Übermitteln zur Überprüfung

Facebook erfordert eine Datenschutzrichtlinien-URL und eine Nutzungsbedingungen-URL auf der Seite mit den Grundeinstellungen der App. Die Seite mit dem [Verhaltenskodex](https://aka.ms/bf-conduct) enthält Links zu Ressourcen von Drittanbietern, die Ihnen beim Erstellen einer Datenschutzrichtlinie helfen. Die Seite [Nutzungsbedingungen](https://aka.ms/bf-terms) enthält Beispielbedingungen zur Unterstützung beim Erstellen eines geeigneten Dokuments mit Nutzungsbedingungen.

Nachdem der Bot fertiggestellt wurde, führt Facebook einen eigenen [Überprüfungsprozess](https://developers.facebook.com/docs/messenger-platform/app-review) für Apps durch, die für Messenger veröffentlicht werden. Der Bot wird getestet, um sicherzustellen, dass er mit den [Plattformrichtlinien](https://developers.facebook.com/docs/messenger-platform/policy-overview) von Facebook konform ist.

## <a name="make-the-app-public-and-publish-the-page"></a>Veröffentlichen der App und der Seite

> [!NOTE]
> Bis zu ihrer Veröffentlichung befindet sich eine App im [Entwicklungsmodus](https://developers.facebook.com/docs/apps/managing-development-cycle). Die Funktionen von Plug-Ins und APIs sind nur für Administratoren, Entwickler und Tester aktiv.

Nach der erfolgreichen Überprüfung legen Sie die App auf dem App-Dashboard unter „App-Prüfung“ als öffentlich fest.
Stellen Sie sicher, dass die Facebook-Seite, der dieser Bot zugeordnet ist, öffentlich ist. Der Status wird in den Seiteneinstellungen angezeigt.

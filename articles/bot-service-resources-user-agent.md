---
title: Bot Framework – Benutzer-Agent-Anforderungen | Microsoft-Dokumentation
description: Grundlegendes zu den Anforderungen von Bot Framework an Ihren Webserver.
author: JohnD-ms
ms.author: v-jodemp
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: befd46cc13fa10a2d1a0f8cf2beed4cb041ab3bd
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49998537"
---
# <a name="bot-framework-user-agent-requests"></a>Bot Framework – Benutzer-Agent-Anforderungen

Wenn Sie diesen Artikel lesen, haben Sie wahrscheinlich eine Anforderung von einem Microsoft Bot Framework-Dienst erhalten. Dieser Leitfaden wird Ihnen helfen, die Anforderung zu verstehen und ihnen zeigen wie Sie diese bei Bedarf beenden können.

Wenn Sie eine Anforderung von einem Microsoft-Dienst erhalten haben, enthielt diese wahrscheinlich einen Benutzer-Agent-Header, der ähnlich der folgenden Zeichenfolge formatiert war:

```User-Agent: BF-DirectLine/3.0 (Microsoft-BotFramework/3.0 +https://botframework.com/ua)```

Der wichtigste Teil dieser Zeichenfolge ist der Bezeichner **Microsoft-BotFramework**, der von Microsoft Bot Framework verwendet wird, einer Sammlung von Tools und Diensten mit der unabhängige Softwareentwickler ihre eigenen Bots erstellen und ausführen können.

Wenn Sie ein Botentwickler sind, wissen Sie vielleicht schon, warum Ihr Dienst diese Anforderungen erhalten hat. Falls nicht, lesen Sie weiter, um mehr zu erfahren.

## <a name="why-is-microsoft-contacting-my-service"></a>Warum kontaktiert Microsoft meinen Dienst?

Bot Framework verbindet Benutzer von Chatdiensten wie Skype und Facebook Messenger mit Bots, d. h. Webservern mit REST-APIs, die auf internetfähigen Endpunkten ausgeführt werden. Die HTTP-Aufrufe an Bots (auch Webhookaufrufe genannt) werden nur an URLs gesendet, die von einem Botentwickler angegeben wurden, der sich im Bot Framework-Entwicklerportal registriert hat.

Wenn Ihr Webdienst unerwünschte Anforderungen von Bot Framework-Diensten erhält, hat wahrscheinlich ein Entwickler versehentlich oder wissentlich Ihre URL als Webhookrückruf für seinen Bot angegeben.

## <a name="to-stop-these-requests"></a>Beenden dieser Anforderungen

Wenn Sie Hilfe benötigen, um unerwünschte Anforderungen an Ihren Webdienst zu unterbinden, wenden Sie sich bitte an [bf-reports@microsoft.com](mailto://bf-reports@microsoft.com).

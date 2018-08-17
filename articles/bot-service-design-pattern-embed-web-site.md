---
title: Einbetten eines Bots in eine Website | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen Bot entwerfen, der in eine Website eingebettet wird.
author: matvelloso
ms.author: mateusv
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 8f50c54c0841db5778c7966e30ec33f89938b376
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301805"
---
# <a name="embed-a-bot-in-a-website"></a>Einbetten eines Bots in eine Website

Bots befinden sich zwar häufig außerhalb von Websites, können aber auch in eine Website eingebettet werden. Sie können beispielsweise einen [Wissens-Bot](~/bot-service-design-pattern-knowledge-base.md) in eine Website einbetten, um Benutzern die schnelle Suche nach Informationen zu ermöglichen, die andernfalls in komplexen Websitestrukturen unter Umständen nur schwer auffindbar sind. Sie können einen Bot auch in eine Helpdeskwebsite einbetten, die dann als erste Anlaufstelle für eingehende Benutzeranfragen fungiert. Der Bot kann unabhängig einfache Probleme lösen und kompliziertere Fälle an einen menschlichen Mitarbeiter [übergeben](~/bot-service-design-pattern-handoff-human.md). 

In diesem Artikel wird untersucht, wie Bots in Websites integriert werden und wie der *Backchannelmechanismus* dazu genutzt wird, die private Kommunikation zwischen einer Webseite und einem Bot zu erleichtern. 

Microsoft bietet zwei verschiedene Möglichkeiten, einen Bot in eine Website zu integrieren: über das Skype-Steuerelement und über ein Open Source-Websteuerelement.

## <a name="skype-web-control"></a>Skype-Websteuerelement

Das Skype-Websteuerelement ist im Wesentlichen ein Skype-Client in einem webfähigen Steuerelement. Die integrierte Skype-Authentifizierung ermöglicht dem Bot das Authentifizieren und Erkennen von Benutzern, ohne dass dazu vom Entwickler benutzerdefinierter Code geschrieben werden muss. Skype erkennt automatisch im Webclient verwendete Microsoft-Konten. 

Da das Skype-Websteuerelement einfach als Front-End für Skype fungiert, hat der Skype-Client des Benutzers automatisch Zugriff auf den vollständigen Kontext aller vom Websteuerelement ermöglichten Konversationen. Auch nach dem Schließen des Webbrowsers kann der Benutzer mithilfe des Skype-Clients weiterhin mit dem Bot interagieren. 

## <a name="open-source-web-control"></a>Open Source-Websteuerelement

Das <a href="https://github.com/Microsoft/BotFramework-WebChat" target="_blank">Open Source-Webchat-Steuerelement</a> basiert auf ReactJS und nutzt die [Direct Line-API][directLineAPI] für die Kommunikation mit Bot Framework. Das Webchat-Steuerelement bietet eine leere Canvas zum Implementieren des Webchats sowie vollständige Kontrolle über das Verhalten und die bereitgestellte Benutzeroberfläche. 

Über den *Backchannelmechanismus* kann die Webseite, auf der das Steuerelement gehostet wird, direkt und für den Benutzer unbemerkt mit dem Bot kommunizieren. Diese Funktion ermöglicht eine Reihe nützlicher Szenarien: 

- Die Webseite kann relevante Daten an den Bot senden (etwa den GPS-Standort).
- Die Webseite kann den Bot über Benutzeraktionen benachrichtigen (Beispiel: „Der Benutzer hat eben Option A in der Dropdownliste ausgewählt.“).
- Die Webseite kann dem Bot das Authentifizierungstoken für einen angemeldeten Benutzer senden.
- Der Bot kann relevante Daten an die Webseite senden (etwa den aktuellen Wert des Benutzerportfolios).
- Der Bot kann „Befehle“ an die Webseite senden (etwa zur Änderung der Hintergrundfarbe).

## <a name="using-the-backchannel-mechanism"></a>Verwenden des Backchannelmechanismus

[!INCLUDE [Introduction to backchannel mechanism](~/includes/snippet-backchannel.md)]

## <a name="sample-code"></a>Beispielcode

Das <a href="https://github.com/Microsoft/BotFramework-WebChat" target="_blank">Open Source-Webchat-Steuerelement</a> ist auf GitHub verfügbar. Weitere Informationen dazu, wie Sie den Backchannelmechanismus mithilfe des Open Source-Webchat-Steuerelements und des Bot Builder SDK für Node.js implementieren können, finden Sie unter [Verwenden des Backchannelmechanismus](~/nodejs/bot-builder-nodejs-backchannel.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Direct Line-API][directLineAPI]
- [TODO](~/dotnet/bot-builder-dotnet-activities.md)
- [Verwenden des Backchannelmechanismus](~/nodejs/bot-builder-nodejs-backchannel.md)

[directLineAPI]: https://docs.botframework.com/en-us/restapi/directline3/#navtitle

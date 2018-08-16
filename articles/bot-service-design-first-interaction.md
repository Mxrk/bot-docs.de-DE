---
title: Entwerfen der ersten Benutzerinteraktion eines Bots | Microsoft-Dokumentation
description: Erfahren Sie, was eine hervorragende erste Benutzerinteraktion ausmacht, und wie Sie Ihre Bots erfolgreich entwerfen.
keywords: erste Eindruck, Start, Sprache vs. Menü
author: matvelloso
ms.author: mateusv
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: d36f043ec3e268b7c56abef7253ddf6274a71a7e
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39302520"
---
# <a name="design-a-bots-first-user-interaction"></a>Entwerfen der ersten Benutzerinteraktion eines Bots

## <a name="first-impressions-matter"></a>Der erste Eindruck zählt

Die erste Interaktion zwischen Benutzer und Bot ist entscheidend für die Benutzerfreundlichkeit. Denken Sie beim Entwerfen Ihres Bots daran, dass die erste Nachricht nicht nur der Begrüßung dient. Wenn Sie eine App erstellen, entwerfen Sie den ersten Bildschirm, um wichtige [Navigationshinweise](bot-service-design-navigation.md) bereitzustellen. Benutzer sollten beispielsweise intuitiv verstehen, wo sich das Menü befindet und wie es funktioniert, wo sie die Hilfe finden, was die Datenschutzrichtlinie ist usw. Wenn Sie einen Bot entwerfen, sollte die erste Benutzerinteraktion mit dem Bot die gleichen Informationen liefern. 

## <a name="language-versus-menus"></a>Sprache vs. Menüs 

Betrachten Sie diese beiden Entwürfe:

### <a name="design-1"></a>Entwurf 1

![Bot](~/media/bot-service-design-first-interaction/hello1.png)


### <a name="design-2"></a>Entwurf 2

![Bot](~/media/bot-service-design-first-interaction/hello2.png)

In der Regel wird nicht empfohlen, den Bot mit einer offenen Frage wie „Wie ich Ihnen helfen?“ zu starten. is generally not recommended. Wenn Ihr Bot hundert verschiedene Optionen umfasst, werden Benutzer die meisten davon wahrscheinlich nicht erraten. Ihr Bot hat ihnen nicht verraten, was er kann – woher sollten sie es also wissen?

Menüs bieten eine einfache Lösung für dieses Problem. Zunächst informiert der Bot den Benutzer über seine Fähigkeiten, indem er die verfügbaren Optionen auflistet. Außerdem ersparen Menüs dem Benutzer viele Eingaben – stattdessen können sie einfach klicken. Schließlich kann das Verwenden von Menüs Ihre natürlichen Sprachmodelle erheblich vereinfachen, indem der Eingabeumfang, den der Bot vom Benutzer erhalten könnte, eingeschränkt wird. 

> [!TIP]
> Menüs sind ein wertvolles Tool beim Entwerfen von Bots, um ausgezeichnete Benutzerfreundlichkeit zu bieten. Sie sollten sie nicht unterschätzen oder als „zu simpel“ betrachten. Sie können Ihren Bot so entwerfen, dass er Menüs verwendet und gleichzeitig Freiformeingaben unterstützt. Wenn ein Benutzer auf das Startmenü reagiert, indem er eine Menüoption eingibt und nicht auswählt, könnte Ihr Bot versuchen, die Texteingabe des Benutzers zu analysieren. 

Alternativ können Sie den Benutzer mit gezielten Fragen führen, falls der Bot eine bestimmte Funktion hat. Wenn Ihr Bot beispielsweise für die Annahme von Sandwichbestellungen zuständig ist, könnte Ihre erste Interaktion folgendermaßen aussehen: „Hallo! Ich nehme Ihre Sandwichbestellung auf. Welches Brot möchten Sie? Wir haben Weißbrot, Weizenbrot und Roggenbrot.“ Auf diese Weise weiß der Benutzer, wie er reagieren muss, und erhält Navigationshinweise in der Unterhaltung.

## <a name="other-considerations"></a>Weitere Überlegungen

Zusätzlich zu einer intuitiven ersten Interaktion, in der Benutzer leicht navigieren können, bietet ein gut gestalteter Bot dem Benutzer Zugriff auf Informationen zu den Datenschutzrichtlinien und Nutzungsbedingungen. 

> [!TIP]
> Wenn Ihr Bot persönliche Daten vom Benutzer sammelt, ist es wichtig, den Benutzer darauf hinzuweisen und darüber zu informieren, was mit den Daten geschieht.

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun mit einigen Grundprinzipien für das Entwerfen der ersten Interaktion zwischen Benutzer und Bot vertraut sind, wäre der nächste Schritt das [Entwerfen des Unterhaltungsflusses](~/bot-service-design-conversation-flow.md).

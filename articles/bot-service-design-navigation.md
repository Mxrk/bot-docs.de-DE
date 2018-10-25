---
title: Entwerfen der Bot-Navigation | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie eine gute Navigationsstruktur für Ihren Bot entwerfen und die häufigsten Fehler beim Navigationsentwurf vermeiden.
keywords: Navigation, Übersicht, eigensinniger Bot, ratloser Bot, rätselhafter Bot, Blitzmerkerbot, Bot, der nicht vergessen kann
author: matvelloso
ms.author: mateusv
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: f2a97b35f7e83a825e533be528951e8c04c521a1
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49998081"
---
# <a name="design-bot-navigation"></a>Entwerfen der Bot-Navigation

Benutzer können in Websites mithilfe von Breadcrumbs, in Apps mithilfe von Menüs und in Webbrowsern mithilfe von Schaltflächen wie **Vorwärts** und **Zurück** navigieren. Jedoch keine dieser etablierten Navigationstechniken entspricht den Navigationsanforderungen eines Bots. Wie [bereits](~/bot-service-design-conversation-flow.md#handle-interruptions) erläutert, kommunizieren Benutzer häufig nicht linear mit Bots. Daher stellt der Entwurf einer durchgängig benutzerfreundlichen Bot-Navigation eine Herausforderung dar. 

Betrachten Sie die folgenden Dilemmas:

- Wie stellen Sie sicher, dass sich ein Benutzer in der Konversation mit einem Bot nicht verliert? 
- Kann ein Benutzer in einer Konversation mit einem Bot zurücknavigieren? 
- Wie navigiert ein Benutzer während einer Konversation mit einem Bot zum „Hauptmenü“? 
- Wie kann ein Benutzer einen Vorgang während einer Konversation mit einem Bot „abbrechen“? 

Die Besonderheiten des Navigationsentwurfs für Ihren Bot hängen weitgehend von den Funktionen ab, die von Ihrem Bot unterstützt werden. Ganz gleich, welche Art von Bot Sie entwickeln, werden Sie die gängigen Fehler von schlecht entworfenen Konversationsschnittstellen vermeiden möchten. In diesem Artikel werden diese Fehler in Bezug auf fünf Persönlichkeiten beschrieben: der „eigensinnige Bot“, der „ratlose Bot“, der „rätselhafte Bot“, der „Blitzmerkerbot“ und der „Bot, der nicht vergessen kann“. 

> [!TIP]
> Die Eigenschaften dieser Persönlichkeiten können Sie für Ihren Bot meist durch eine ordnungsgemäße [Behandlung von Benutzerunterbrechungen](v4sdk/bot-builder-howto-handle-user-interrupt.md) abschwächen.

## <a name="the-stubborn-bot"></a>Der „eigensinnige Bot“

Der eigensinnige Bot besteht darauf, den aktuellen Gesprächsverlauf beizubehalten, auch wenn der Benutzer versucht, in eine andere Richtung zu lenken. 

Stellen Sie sich folgendes Szenario vor: 

![Bot](~/media/bot-service-design-navigation/stubborn-bot-new.png)

Benutzer entscheiden sich häufig um, möchten einen Vorgang abbrechen oder noch einmal von vorne beginnen. 

> [!TIP]
> <b>Empfohlene Vorgehensweise</b>: Entwerfen Sie Ihren Bot so, dass ein Benutzer den Gesprächsverlauf jederzeit ändern kann. 
>
> <b>Nicht empfohlene Vorgehensweise</b>: Entwerfen Sie Ihren Bot so, dass Benutzereingaben ignoriert werden und dieselbe Frage in einer Endlosschleife wiederholt wird. 

Es gibt viele Möglichkeiten, diesen Fehler zu vermeiden. Indem Sie für jede Frage einfach eine maximale Anzahl von Wiederholungsversuchen festlegen, können Sie vielleicht am einfachsten verhindern, dass ein Bot dieselbe Frage in einer Endlosschleife wiederholt. Wenn Sie den Bot auf diese Weise entwerfen, agiert der Bot nicht „intelligent“, um die Benutzereingabe zu verstehen und entsprechend zu reagieren, vermeidet aber zumindest, dass dieselbe Frage in einer Endlosschleife wiederholt wird. 

## <a name="the-clueless-bot"></a>Der „ratlose Bot“

Der ratlose Bot reagiert widersinnig, wenn er den Versuch eines Benutzers, auf eine bestimmte Funktion zuzugreifen, nicht versteht. Ein Benutzer kann gängige Schlüsselwortbefehle wie „Hilfe“ oder „Abbrechen“ ausprobieren. Die Chancen stehen gut, dass der Bot entsprechend reagiert.

Stellen Sie sich folgendes Szenario vor: 

![Bot](~/media/bot-service-design-navigation/clueless-bot.png)

Obwohl Sie möglicherweise versucht sein werden, jeden Dialog in Ihrem Bot so zu entwerfen, dass auf bestimmte Schlüsselwörter geachtet und auf diese entsprechend reagiert wird, wird dieses Konzept nicht empfohlen. 

> [!TIP]
> <b>Empfohlene Vorgehensweise</b>: Implementieren Sie [Middleware](v4sdk/bot-builder-create-middleware.md), mit der in der Benutzereingabe nach den von Ihnen angegebenen Schlüsselwörter (wie „Hilfe“, „Abbrechen“, „Neustart“ usw.) gesucht und entsprechend reagiert wird. 
> 
> <b>Nicht empfohlene Vorgehensweise</b>: Entwerfen Sie jeden Dialog so, dass Benutzereingaben anhand einer Liste mit Schlüsselwörtern überprüft werden. 

Dadurch, dass Sie die Logik in Ihrer **Middleware** definieren, kann bei jedem Austausch mit dem Benutzer darauf zugegriffen werden. Mit diesem Konzept können einzelne Dialoge und Aufforderungen so entworfen werden, dass die Schlüsselwörter bei Bedarf problemlos ignoriert werden.

## <a name="the-mysterious-bot"></a>Der „rätselhafte Bot“

Der rätselhafte Bot kann Benutzereingaben nie sofort bestätigen. 

Stellen Sie sich folgendes Szenario vor: 

![Bot](~/media/bot-service-design-navigation/mysterious-bot.png)

Diese Situation kann unter Umständen ein Hinweis darauf sein, dass der Bot ausgefallen ist. Es ist allerdings auch möglich, dass der Bot lediglich mit der Verarbeitung der Benutzereingabe beschäftigt ist und die Kompilierung seiner Reaktion noch nicht abgeschlossen hat. 

> [!TIP]
> <b>Empfohlene Vorgehensweise</b>: Entwerfen Sie Ihren Bot so, dass er Benutzereingaben sofort bestätigt, auch wenn er einige Zeit zum Kompilieren einer Reaktion benötigt. 
> 
> <b>Nicht empfohlene Vorgehensweise</b>: Entwerfen Sie Ihren Bot so, dass er die Bestätigung von Benutzereingaben bis nach der Kompilierung einer Reaktion zurückstellt.

Durch eine sofortige Bestätigung von Benutzereingaben werden Unsicherheiten in Bezug auf den Zustand des Bots vermieden. Wenn die Kompilierung einer Reaktion längere Zeit in Anspruch nimmt, sollten Sie als Hinweis darauf, dass der Bot beschäftigt ist, eine „Eingabemeldung“ und anschließend eine [proaktive Meldung](v4sdk/bot-builder-howto-proactive-message.md) senden.

## <a name="the-captain-obvious-bot"></a>Der „Blitzmerkerbot“

Der Blitzmerkerbot liefert ungefragte Informationen, die absolut offensichtlich und somit für den Benutzer nutzlos sind. 

Stellen Sie sich folgendes Szenario vor:

![Bot](~/media/bot-service-design-navigation/captainobvious-bot.png)

> [!TIP]
> <b>Empfohlene Vorgehensweise</b>: Entwerfen Sie Ihren Bot so, dass er für den Benutzer nützliche Informationen liefert. 
> 
> <b>Nicht empfohlene Vorgehensweise</b>: Entwerfen Sie Ihren Bot so, dass er ungefragte Informationen liefert, die für den Benutzer sehr wahrscheinlich nutzlos sind.

Indem Sie Ihren Bot so entwerfen, dass er nützliche Informationen liefert, erhöhen Sie die Wahrscheinlichkeit, dass der Benutzer Ihren Bot nutzen wird.

## <a name="the-bot-that-cant-forget"></a>Der „Bot, der nicht vergessen kann“

Der Bot, der nicht vergessen kann, integriert unpassenderweise Informationen aus früheren Konversationen in die aktuelle. 

Stellen Sie sich folgendes Szenario vor:

![Bot](~/media/bot-service-design-navigation/rememberall-bot.png)

> [!TIP]
> <b>Empfohlene Vorgehensweise</b>: Entwerfen Sie Ihren Bot so, dass das aktuelle Gesprächsthema beibehalten wird, bis der Benutzer den Wunsch äußert, zu einem früheren Thema zurückkehren zu wollen. 
> 
> <b>Nicht empfohlene Vorgehensweise</b>: Entwerfen Sie Ihren Bot so, dass Informationen aus früheren Konversationen eingestreut werden, wenn dies für die aktuelle Konversation nicht relevant ist.

Durch die Beibehaltung des aktuellen Gesprächsthemas vermeiden Sie Fragen und Frustration und erhöhen die Wahrscheinlichkeit, dass der Benutzer Ihren Bot weiterhin nutzt.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie Ihren Bot so entwerfen, dass diese gern gemachten Fehler aufgrund von schlecht entworfenen Konversationsschnittstellen vermieden werden, ist dies ein wichtiger Schritt zur Gewährleistung eines benutzerfreundlichen Bots. 

Als Nächstes lernen Sie die [UX-Elemente](~/bot-service-design-user-experience.md) kennen, die Bots in der Regel für den Austausch von Informationen mit Benutzern verwenden. 

---
title: Übergeben von Unterhaltungen von einem Bot an einen Menschen | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie den Entwurf auf Situationen ausrichten, bei denen ein Benutzer eine Unterhaltung mit einem Bot startet, die dann an einen Menschen übergeben werden muss.
author: matvelloso
ms.author: mateusv
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: f786f79011da5e50b37f9797dca694f0e132296c
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39302365"
---
# <a name="transition-conversations-from-bot-to-human"></a>Übergeben von Unterhaltungen von einem Bot an einen Menschen

Ganz gleich, wie groß das Ausmaß künstlicher Intelligenz bei einem Bot ist – es kann dennoch zu Situationen kommen, in denen die Unterhaltung an einen Menschen übergeben werden muss. Der Bot sollte erkennen, wann eine solche Übergabe erforderlich ist, und dem Benutzer eine klare und reibungslose Übergabe ermöglichen.

## <a name="scenarios-that-require-human-involvement"></a>Szenarien, die menschliches Eingreifen erfordern

Bei einer Vielzahl von Szenarien kann es vorkommen, dass ein Bot die Kontrolle über die Unterhaltung an einen Menschen übergeben muss. Zu solchen Szenarien zählen u.a. die *Selektierung*, *Eskalation* und *Überwachung*. 

### <a name="triage"></a>Eingrenzung

Eine typische Helpdeskanfrage beginnt mit ein paar grundlegenden Fragen, die problemlos von einem Bot beantwortet werden können. Als erster reagierender Kontaktpunkt für eingehende Anfragen von Benutzern könnte ein Bot den Namen des Benutzers, die Adresse, die Beschreibung des Problems oder andere relevante Informationen sammeln und die Kontrolle über die Unterhaltung anschließend an einen Mitarbeiter übergeben. Anstatt sich mit der Sammlung von Informationen befassen zu müssen, können sich Mitarbeiter mithilfe eines Bots zum Selektieren von eingehenden Anfragen auf die Lösung des Problems konzentrieren.

### <a name="escalation"></a>Eskalation

Bei dem Szenario des Helpdesks kann ein Bot neben der Sammlung von Informationen eventuell grundlegende Fragen beantworten und einfache Probleme beheben, wie etwa das Kennwort eines Benutzers zurücksetzen. Wenn sich im Zuge einer Unterhaltung jedoch abzeichnen sollte, dass das Problem eines Benutzers so komplex ist, dass menschliches Eingreifen erforderlich ist, muss der Bot das Problem an einen Mitarbeiter eskalieren. Um diese Art von Szenario zu implementieren, muss ein Bot zwischen Problemen, die er eigenständig lösen kann, und Problemen, die an einen Menschen eskaliert werden müssen, differenzieren können. Es gibt eine Vielzahl von Möglichkeiten, mit denen ein Bot ermitteln kann, dass die Kontrolle über die Unterhaltung an einen Menschen übergeben werden muss. 

#### <a name="user-driven-menus"></a>Benutzergesteuerte Menüs

Die womöglich einfachste Möglichkeit für einen Bot, mit diesem Dilemma umzugehen, besteht darin, dem Benutzer ein Menü mit Optionen anzuzeigen. Aufgaben, die der Bot eigenständig bearbeiten kann, werden im Menü oberhalb eines Links mit der Bezeichnung „Chat with an agent“ angezeigt. Diese Art der Implementierung erfordert keine erweiterte Machine Learning-Aktivität oder Analyse natürlicher Sprache. Der Bot überträgt die Kontrolle über die Unterhaltung einfach an einen Mitarbeiter, wenn der Benutzer die Option „Chat with an agent“ auswählt. 

#### <a name="scenario-driven"></a>Szenariogesteuerte Menüs

Der Bot kann entscheiden, ob die Kontrolle auf Grundlage dessen übertragen werden soll, ob er das betreffende Szenario selbst verarbeiten kann. Der Bot sammelt einige Informationen zur Anfrage des Benutzers und fragt dann seine interne Liste von Funktionen ab, um festzustellen, ob er imstande ist, diese Anfrage zu bearbeiten. Stellt der Bot fest, dass er die jeweilige Anfrage behandeln kann, so führt er die entsprechenden Aktionen aus. Ist jedoch Gegenteiliges der Fall, übergibt er die Kontrolle über die Unterhaltung an einen Mitarbeiter.

#### <a name="natural-language"></a>Analyse natürlicher Sprache

Mit Unterstützung der Analyse natürlicher Sprache und der Stimmungsanalyse kann der Bot entscheiden, wann die Kontrolle über die Unterhaltung an einen Mitarbeiter übertragen werden muss. Dies ist besonders hilfreich, wenn ermittelt werden soll, ob der Benutzer frustriert ist oder mit einem Mitarbeiter sprechen möchte. 
 
Der Bot analysiert den Inhalt der Benutzernachrichten mithilfe der <a href="https://www.microsoft.com/cognitive-services/en-us/text-analytics-api" target="blank">Textanalyse-API</a> zum Ableiten der Stimmung oder mithilfe der <a href="https://www.luis.ai" target="_blank">LUIS-API</a>. 


> [!TIP]
> Die Analyse natürlicher Sprache muss nicht immer die beste Methode darstellen, um zu bestimmen, wann ein Bot die Kontrolle über eine Unterhaltung an einen Menschen übertragen sollte. Denn Bots treffen wie Menschen nicht immer die richtige Entscheidung und ungültige Antworten sind für Benutzer frustrierend. Trifft der Benutzer jedoch eine Auswahl aus einem Menü mit gültigen Optionen, kann der Bot immer eine geeignete Reaktion für die jeweilige Eingabe liefern. 

### <a name="supervision"></a>Überwachung

In einigen Fällen sollte ein Mitarbeiter lediglich die Unterhaltung überwachen, statt die Kontrolle zu übernehmen.

Denken Sie beispielsweise an ein Helpdeskszenario, bei dem ein Bot bezüglich der Diagnose von Computerproblemen mit einem Benutzer kommuniziert. Durch ein Machine Learning-Modell kann der Bot die wahrscheinlichste Ursache für das Problem ermitteln. Bevor der Bot dem Benutzer jedoch eine bestimmte Vorgehensweise empfiehlt, kann er die Diagnose vertraulich bestätigen, den Mitarbeiter informieren und zur Fortsetzung die Autorisierung anfordern. Der Mitarbeiter klickt dann auf eine Schaltfläche, der Bot stellt dem Benutzer die entsprechende Lösung vor und das Problem ist gelöst. Der Bot führt zwar nach wie vor den Großteil der Arbeit aus, doch die endgültige Entscheidung bleibt in der Hand des Mitarbeiters. 

## <a name="transitioning-control-of-the-conversation"></a>Übertragen der Kontrolle über die Unterhaltung 

Wenn ein Bot die Entscheidung trifft, die Kontrolle über eine Unterhaltung an einen Mitarbeiter zu übertragen, kann er den Benutzer darüber informieren, dass das jeweilige Problem eskaliert wird, und dass die Unterhaltung in den Wartezustand versetzt wird, bis ein Mitarbeiter verfügbar ist. 

Wenn der Bot auf einen Mitarbeiter wartet, können alle eingehenden Benutzernachrichten automatisch mit einer Standardantwort beantwortet werden, z.B. mit „waiting in queue“. Darüber hinaus können Sie veranlassen, dass der Bot den Wartezustand der Unterhaltung aufhebt, wenn der Benutzer bestimmte Nachrichten gesendet hat (z.B. „never mind“ oder „cancel“).

Beim Entwurf Ihres Bots geben Sie an, wie Mitarbeiter den wartenden Benutzern zugewiesen werden. So kann der Bot z.B. ein einfaches Warteschlangensystem nach dem Prinzip „First In, First Out“ implementieren. Bei einer komplexeren Logik würden Benutzer basierend auf Geografie, Sprache oder anderen Faktoren den Mitarbeitern zugewiesen werden. Zudem könnte der Bot eine Art von Benutzeroberfläche für Mitarbeiter bereitstellen, über die sie einen Benutzer auswählen können. Wenn ein Mitarbeiter verfügbar wird, wird eine Verbindung mit dem Bot hergestellt und er tritt der Unterhaltung bei.

> [!IMPORTANT]
> Auch nachdem ein Mitarbeiter hinzugezogen wurde, bleibt der Bot während der Unterhaltung als Vermittler im Hintergrund. Benutzer und Mitarbeiter kommunizieren nicht direkt miteinander, sondern leiten lediglich Nachrichten über den Bot weiter. 

## <a name="routing-messages-between-user-and-agent"></a>Weiterleiten von Nachrichten zwischen Benutzer und Mitarbeiter

Nachdem der Mitarbeiter eine Verbindung mit dem Bot hergestellt hat, beginnt der Bot mit der Weiterleitung von Nachrichten zwischen Benutzer und Mitarbeiter. Obwohl es den Anschein erweckt, dass Benutzer und Mitarbeiter direkt miteinander chatten, tauschen sie in Wirklichkeit lediglich Nachrichten über den Bot aus. Der Bot empfängt Nachrichten vom Benutzer, die er an den Mitarbeiter sendet, und empfängt wiederum Nachrichten vom Mitarbeiter, die er an den Benutzer sendet. 

> [!NOTE]
> In ausgereifteren Szenarien kann der Bot ein größere Verantwortung als lediglich das Weiterleiten von Nachrichten zwischen Benutzer und Mitarbeiter übernehmen. Beispielsweise kann der Bot entscheiden, welche Antwort geeignet ist und den Mitarbeiter zum Fortsetzen des Vorgangs einfach um eine Bestätigung bitten.

## <a name="sample-code"></a>Beispielcode

Ein vollständiges Beispiel, das zeigt, wie Unterhaltungen mit dem Bot Builder SDK für Node.js vom Bot an einen Benutzer übergeben werden können, finden Sie in GitHub im <a href="https://github.com/palindromed/Bot-HandOff" target="_blank">Beispiel „Bot-HandOff“</a>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

::: moniker range="azure-bot-service-4.0"

- [Dialoge](v4sdk/bot-builder-dialog-manage-conversation-flow.md)
- <a href="https://www.microsoft.com/cognitive-services/en-us/text-analytics-api" target="blank">Textanalyse-API</a>

::: moniker-end

::: moniker range="azure-bot-service-3.0"

- [Verwalten des Unterhaltungsflusses mit Dialogen (.NET)](~/dotnet/bot-builder-dotnet-manage-conversation-flow.md)
- [Verwalten des Unterhaltungsflusses mit Dialogen (Node.js)](~/nodejs/bot-builder-nodejs-manage-conversation-flow.md)
- <a href="https://www.microsoft.com/cognitive-services/en-us/text-analytics-api" target="blank">Textanalyse-API</a>


::: moniker-end


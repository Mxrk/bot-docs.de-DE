---
title: Ereignishandler | Microsoft Docs
description: Informationen zur Verwendung von Ereignishandlern.
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 06/14/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 0c054b8f4209004806e4564be45e83bdf3a8ec25
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39303640"
---
# <a name="event-handlers"></a>Ereignishandler

Ereignishandler sind Funktionen, die wir zukünftigen Aktivitätsereignissen in einem [Durchgang](bot-builder-basics.md#defining-a-turn) hinzufügen können. Diese Aktivitäten sind `SendActivity`, `UpdateActivity` und `DeleteActivity`, und jede von ihnen besitzt einen eigenen Handler. Diese Handler sind nützlich, wenn Sie bei jeder zukünftigen Aktivität dieses Typs für das aktuelle Kontextobjekt eine Aktion ausführen müssen.

Sie können jedem Aktivitätsereignis mehrere Ereignisbehandler hinzufügen. Sie führen die Verarbeitung für jedes Ereignis aus, **nachdem** sie hinzugefügt wurden. Daher ist es wichtig, wo sie im Code hinzugefügt werden.

> [!NOTE]
> *Ereignishandler* in diesem Artikel sind nicht mit den traditionellen sprachspezifischen Definitionen von *Ereignissen* und *Handlern* konform. Stattdessen beziehen sie sich auf die eher konzeptuelle Programmieridee der *Ereignisse* und *Handler*.

## <a name="using-the-next-delegate"></a>Verwenden des *nächsten* Delegaten

Durch Aufrufen des *nächsten* Delegaten übergibt jeder Handler die Ausführung an den folgenden Handler in der Reihenfolge, in der die Handler hinzugefügt wurden. Die letzte *nächste* Delegat ist ein Aufruf des Adapters zum tatsächlichen Senden, Aktualisieren oder Löschen der Aktivität.

Wenn nicht anders beabsichtigt, ist es wichtig, *next()* in Ihrem Handler aufzurufen, andernfalls werden Sie die Aktivität [kurzschließen](bot-builder-create-middleware.md#short-circuit-routing). Der Unterschied zwischen dem Kurzschließen einer Aktivität im Vergleich zu Middleware besteht darin, dass ein Kurzschluss die Aktivität vollständig abbricht, während die Middleware ändert, welcher Code ausgeführt werden darf, aber die Ausführung der Aktivität fortgesetzt wird.

Bei Sendeaktivitäten gibt der *nächste* Delegat bei Erfolg die ID(s) der gesendeten Nachricht(en) zurück.

## <a name="expected-return-value"></a>Erwarteter Rückgabewert

Als Rückgabewert erwartet der Ereignishandler das Ergebnis des nächsten Delegaten. Bei Bedarf kann das Ergebnis vor der Rückgabe angezeigt und gespeichert werden, oder es kann direkt wie unten gezeigt zurückgegeben werden.

Der Rückgabewert des `SendActivity`-Handlers ist der Rückgabewert, der als Rückgabewert des entsprechenden nächsten Delegaten zurück durch die Kette übergeben wird.

# <a name="ctabcseventhandler"></a>[C#](#tab/cseventhandler)

Die drei Typen von Ereignishandlern, die bereitgestellt werden, sind `OnSendActivities()`, `OnUpdateActivity()` und `OnDeleteActivity()`. Hier fügen wir einen `OnSendActivities()`-Handler innerhalb unseres Botcodes hinzu, aber Handler können auch im Middlewarecode hinzugefügt werden.

Handler werden häufig als Lambda-Ausdrücke hinzugefügt, wie Sie in diesem Beispiel sehen können. Hier lauschen wir auf die Eingabeaktivität des Benutzers, wenn **help** eingegeben wird.

```cs
public Task OnTurn(ITurnContext context)
{
    if (context.Activity.Type == ActivityTypes.Message)
    {
        // Get the conversation state from the turn context
        
        context.OnSendActivities(async (handlerContext, activities, handlerNext) =>
        {
            if (handlerContext.Activity.Text == "help")
            {
                Console.WriteLine("help!");
                // Do whatever logging you want to do for this help message
            }

            return await handlerNext();
        });
        // ...
    }
}
```

# <a name="javascripttabjseventhandler"></a>[JavaScript](#tab/jseventhandler)

Die drei Typen von Ereignishandlern, die bereitgestellt werden, sind `onSendActivities()`, `onUpdateActivity()` und `onDeleteActivity()`. Hier fügen wir einen `onSendActivities()`-Handler in unserem Botcode hinzu, Handler können jedoch auch als Middlewarecode hinzugefügt werden.

In diesem Beispiel wird auf die Eingabeaktivität des Benutzers gelauscht, wenn **help** eingegeben wird.

```js
adapter.processActivity(req, res, async (context) => {

    if (context.activity.type === 'message') {

        context.onSendActivities(async (handlerContext, activities, handlerNext) => { 
            
            if(handlerContext.activity.text === 'help'){
                console.log('help!')
                // Do whatever logging you want to do for this help message
            }
            // Add handler logic here
        
            await handlerNext(); 
        });
        await context.sendActivity(`you said ${context.activity.text}`);
    }
});
```

---

Es ist wichtig, zwischen Ereignissen einer *Sendeaktivität* und einer *Aktualisierungs- oder Löschaktivität* zu unterscheiden, weil der erste Typ ein völlig neues Aktivitätsereignis erstellt und der zweite Typ eine Aktion für eine vergangene Aktivität ausführt. Außerdem gilt: Nicht alle Kanäle unterstützen eine *Aktualisierungs-* oder *Lösch*aktivität. Es wird empfohlen, die entsprechende Ausnahmebehandlung für Aufrufe dieser Aktivitäten und ihre Handlern hinzuzufügen, um diese Möglichkeit zu berücksichtigen.


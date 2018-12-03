---
title: Erstellen eigener Middleware | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihre eigene Middleware schreiben.
keywords: middleware, custom middleware, short circuit, fallback, activity handlers
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 03/21/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 78c0afd6095cf9a10e04e1b0131d66ce6964f369
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "50000197"
---
# <a name="create-your-own-middleware"></a>Erstellen eigener Middleware

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Mithilfe von Middleware können Sie umfassende Plug-Ins für Ihre Bots schreiben, die auch von anderen Benutzern verwendet werden können. In diesem Artikel wird gezeigt, wie grundlegende Middleware hinzugefügt und implementiert wird und wie diese funktioniert. Das SDK v4 stellt Middleware für Sie zur Verfügung, z.B. die Zustandsverwaltung, die LUIS-App, QnAMaker und Übersetzung. Werfen Sie einen Blick auf den Bot Builder SDK für [.NET](https://github.com/Microsoft/botbuilder-dotnet) oder [JavaScript](https://github.com/Microsoft/botbuilder-js), um weitere Informationen zu erhalten.

## <a name="adding-middleware"></a>Hinzufügen von Middleware

Im folgenden Beispiel, das auf dem in den [ersten Schritten](~/bot-service-quickstart.md) erstellten einfachen Bot-Beispiel basiert, werden unseren Diensten zwei unterschiedliche Middlewareplattformen mit einer neuen Instanz jeder dieser Klassen hinzugefügt.

> [!IMPORTANT]
> Beachten Sie, dass die Reihenfolge, in der sie den Optionen hinzugefügt werden, die Reihenfolge darstellt, in der sie ausgeführt werden. Achten Sie darauf, wie sich die Verwendung von mehreren Middlewareplattformen auf Ihre Arbeit auswirkt.

**Startup.cs**

# <a name="ctabcsaddmiddleware"></a>[C#](#tab/csaddmiddleware)

Fügen Sie für jede Middlewareplattform, die Sie hinzufügen möchten, `options.Middleware.Add(new MyMiddleware());`-Methodenaufrufe zu Ihren Bot-Dienstoptionen hinzu.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton(_ => Configuration);
    services.AddBot<HelloBot>(options =>
    {
        options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);
        options.Middleware.Add(new MyMiddleware());
        options.Middleware.Add(new MyOtherMiddleware());
    });
}
```
# <a name="javascripttabjsaddmiddleware"></a>[JavaScript](#tab/jsaddmiddleware)

Fügen Sie für jede Middlewareplattform, die Sie hinzufügen möchten, `adapter.use(MyMiddleware());` zu Ihrem Adapter hinzu.

```javascript
// Create adapter
const adapter = new botbuilder.BotFrameworkAdapter({
    appId: process.env.MICROSOFT_APP_ID, 
    appPassword: process.env.MICROSOFT_APP_PASSWORD 
});

adapter.use(MyMiddleware());
adapter.use(MyOtherMiddleware());
```

---


## <a name="implementing-your-middleware"></a>Implementieren Ihrer Middleware

Jede Middlewareplattform erbt von einer Middlewareschnittstelle und implementiert immer den jeweiligen Verarbeitungshandler, der für jede Aktivität ausgeführt wird, die an Ihren Bot gesendet wird. Für jede hinzugefügte Middlewareplattform erhält der Verarbeitungshandler die Möglichkeit, das Kontextobjekt zu ändern oder eine Aufgabe auszuführen (z.B. die Protokollierung), bevor eine andere Middlewareplattform oder eine andere Bot-Logik mit dem Kontextobjekt interagieren kann, während die Verarbeitung über die Pipeline fortgesetzt wird.

# <a name="ctabcsetagoverwrite"></a>[C#](#tab/csetagoverwrite)

Jede Middlewareplattform erbt von `IMiddleware` und implementiert immer `OnTurn()`.

**ExampleMiddleware.cs**
```csharp
public class MyMiddleware : IMiddleware
{
    public async Task OnTurn(ITurnContext context, MiddlewareSet.NextDelegate next)
    {            
        // This simple middleware reports the request type and if we responded
        await context.SendActivity($"Request type: {context.Activity.Type}");
        
        await next();            

        // Report if any responses were recorded
        string response = context.Responded ? "yes" : "no";
        await context.SendActivity($"Responded?  {response}");
    }
}

public class MyOtherMiddleware : IMiddleware
{
    public async Task OnTurn(ITurnContext context, MiddlewareSet.NextDelegate next)
    {
        // simple middleware to add an additional send activity
        await context.SendActivity($"My other middleware just saying Hi before the bot logic");

        await next();
    }
}

```

# <a name="javascripttabjsimplementmiddleware"></a>[JavaScript](#tab/jsimplementmiddleware)

Jede Middlewareplattform erbt von `MiddlewareSet` und implementiert immer `onTurn()`.

**ExampleMiddleware.js**
```js
adapter.use({onTurn: async (context, next) =>{

    // This simple middleware reports the activity type and if we responded
    await context.sendActivity(`Activity type: ${context.activity.type}`); 
    await next();            

    // Report if any responses were recorded
    const response = context.activity.text ? "yes" : "no";
    await context.sendActivity(`Responded?  ${response}`);

}}, {onTurn: async (context, next) => {

     // simple middleware to add an additional send activity
     await context.sendActivity("My other middleware just saying Hi before the bot logic");

     await next();
}})
```

---

Das Aufrufen von `next()` bewirkt, dass die Ausführung mit der nächsten Middlewareplattform fortfährt. Die Möglichkeit, den Zeitpunkt auszuwählen, wann die Ausführung übergeben wird, ermöglicht es Ihnen, Code zu schreiben, der **nach** der Ausführung des restlichen Middlewarestapels ausgeführt wird. Nachdem die Bot-Logik und sonstigen Middlewareplattformen ausgeführt wurden, können wir Aktionen für den „hinteren Rand“ des Verarbeitungshandlers durchführen.  Im obigen Beispiel hat die erste implementierte Middlewareplattform genau dies ausgeführt. Dabei wurde gemeldet, wenn wir für dieses Kontextobjekt geantwortet haben, bevor die Ausführung wieder an die Pipeline übergeben wurde.

## <a name="short-circuit-routing"></a>Verkürzte Weiterleitung

In einigen Fällen empfiehlt es sich, die weitere Verarbeitung der empfangenen Aktivität anzuhalten, was als Verkürzung bezeichnet wird. Dies ist in Fällen hilfreich, in denen die Middleware vollständig eine Anforderung übernimmt, eine einfache Antwort für bestimmte Befehle bereitstellt oder eine eingehende Anforderung anderweitig verarbeiten kann, ohne dass sie die Bot-Logik durchlaufen muss.

Erstellen Sie nun eine Middlewareplattform, die eine Antwort sendet und immer eine weitere Weiterleitung der Anforderung verhindert, wenn der Benutzer „ping“ sagt:

# <a name="ctabcsmiddlewareshortcircuit"></a>[C#](#tab/csmiddlewareshortcircuit)
```cs
public class ExampleMiddleware : IMiddleware
{
    public async Task OnTurn(ITurnContext context, MiddlewareSet.NextDelegate next)
    {
        var utterance = context.Activity?.AsMessageActivity()?.Text.Trim().ToLower();

        if (utterance == "ping") 
        {
            context.SendActivity("pong");
            return;
        } 
        else 
        {
            await next();
        }
    }
}
```
# <a name="javascripttabjsmiddlewareshortcircuit"></a>[JavaScript](#tab/jsmiddlewareshortcircuit)
```JavaScript
adapter.use({onTurn: async (context, next) =>{
    const utterance = (context.activity.text || '').trim().toLowerCase();
        if (utterance == "ping") 
        {
            await context.sendActivity("pong");
            return;
        } 
        else 
        {
            await next();
        }

}})

```

---

## <a name="fallback-processing"></a>Fallbackverarbeitung

Darüber hinaus müssen Sie möglicherweise auf eine Anforderung antworten, auf die noch nicht geantwortet wurde. Dies kann mühelos mithilfe des „hinteren Rands“ des Verarbeitungshandlers erreicht werden, indem die Eigenschaft `context.Responded` aktiviert wird. Erstellen Sie nun eine einfache Middlewareplattform, die automatisch „I didn't understand“ sagt, falls der Bot die Anforderung nicht verarbeiten kann:

# <a name="ctabcsfallback"></a>[C#](#tab/csfallback)
```cs
public async Task OnTurn(ITurnContext context, MiddlewareSet.NextDelegate next)
{
    await next();

    if (!context.Responded) 
    {
        context.SendActivity("I didn't understand.");
    }
}
```
# <a name="javascripttabjsfallback"></a>[JavaScript](#tab/jsfallback)
```JavaScript
adapter.use({onTurn: async (context, next) =>{
    await next();

    if (!context.responded) 
    {
       await context.sendActivity("I didn't understand.");
    }

}})
```

---

> [!NOTE] 
> Dies funktioniert möglicherweise nicht in allen Fällen, z.B., wenn andere Middlewareplattformen auf den Benutzer reagieren können oder wenn der Bot ordnungsgemäß eine Nachricht empfängt, aber nicht antwortet. Eine Antwort wie „I don't understand“ wäre in diesem Fall irreführend für unseren Benutzer.



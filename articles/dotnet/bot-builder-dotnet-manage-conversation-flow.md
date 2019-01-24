---
title: Verwalten des Unterhaltungsablaufs mit Dialogen | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Konversationen modellieren und den Konversationsfluss mithilfe von Dialogen und dem Bot Framework SDK für .NET verwalten.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: cfb849474c23c62a666e013c700b755519c1868a
ms.sourcegitcommit: c6ce4c42fc56ce1e12b45358d2c747fb77eb74e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54453805"
---
# <a name="manage-conversation-flow-with-dialogs"></a>Verwalten des Unterhaltungsablaufs mit Dialogen

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-manage-conversation-flow.md)
> - [Node.js](../nodejs/bot-builder-nodejs-dialog-manage-conversation-flow.md)

[!INCLUDE [Dialog flow example](../includes/snippet-dotnet-manage-conversation-flow-intro.md)]

In diesem Artikel wird die Modellierung dieses Konversationsflusses mithilfe von [Dialogen](bot-builder-dotnet-dialogs.md) und des Bot Framework SDK für .NET beschrieben. 

## <a name="invoke-the-root-dialog"></a>Aufrufen des Stammdialogs

Zunächst ruft der Botcontroller den „Stammdialog“ auf. Das folgende Beispiel zeigt, wie der einfache HTTP POST-Aufruf mit einem Controller verbunden und dann der Stammdialog aufgerufen wird. 

```cs
public class MessagesController : ApiController
{
    public async Task<HttpResponseMessage> Post([FromBody]Activity activity)
    {
            // Redirect to the root dialog.
            await Conversation.SendAsync(activity, () => new RootDialog()); 
            ...
    }
}
```

## <a name="invoke-the-new-order-dialog"></a>Aufrufen des Dialogs „New Order“ (neue Bestellung)

Im nächsten Schritt ruft der Stammdialog den Dialog „New Order“ auf. 

```cs
[Serializable]
public class RootDialog : IDialog<object>
{
    public async Task StartAsync(IDialogContext context)
    {
        // Root dialog initiates and waits for the next message from the user. 
        // When a message arrives, call MessageReceivedAsync.
        context.Wait(this.MessageReceivedAsync); 
    }

    public virtual async Task MessageReceivedAsync(IDialogContext context, IAwaitable<IMessageActivity> result)
    {
        var message = await result; // We've got a message!
        if (message.Text.ToLower().Contains("order"))
        {
            // User said 'order', so invoke the New Order Dialog and wait for it to finish.
            // Then, call ResumeAfterNewOrderDialog.
            await context.Forward(new NewOrderDialog(), this.ResumeAfterNewOrderDialog, message, CancellationToken.None);
        }
        // User typed something else; for simplicity, ignore this input and wait for the next message.
        context.Wait(this.MessageReceivedAsync);
    }

    private async Task ResumeAfterNewOrderDialog(IDialogContext context, IAwaitable<string> result)
    {
        // Store the value that NewOrderDialog returned. 
        // (At this point, new order dialog has finished and returned some value to use within the root dialog.)
        var resultFromNewOrder = await result;

        await context.PostAsync($"New order dialog just told me this: {resultFromNewOrder}");

        // Again, wait for the next message from the user.
        context.Wait(this.MessageReceivedAsync);
    }
}
```

## <a id="dialog-lifecycle"></a> Dialoglebenszyklus

Wenn ein Dialog aufgerufen wird, übernimmt er die Steuerung des Unterhaltungsablaufs. Jede neue Nachricht wird durch diesen Dialog verarbeitet, bis er geschlossen oder auf einen anderen Dialog umgeleitet wird. 

In C# können Sie `context.Wait()` verwenden, um den Rückruf anzugeben, der beim nächsten Senden einer Nachricht durch den Benutzer aufgerufen werden soll. Um einen Dialog zu schließen und ihn vom Stapel zu entfernen (und dadurch den Benutzer wieder an den im Stapel vorhergehenden Dialog zu übergeben), verwenden Sie `context.Done()`. Sie müssen jede Dialogmethode mit `context.Wait()`, `context.Fail()`, `context.Done()` oder einer Umleitungsanweisung wie `context.Forward()` oder `context.Call()` beenden. Eine Dialogmethode, die nicht mit einer dieser Anweisungen endet, führt zu einem Fehler (da das Framework nicht weiß, welche Aktion ausgeführt werden soll, wenn der Benutzer die nächste Nachricht sendet).

## <a name="passing-state-between-dialogs"></a>Übergeben des Status zwischen Dialogen

Einerseits können Sie den Status im Botstatus speichern, andererseits können Sie durch Überladen des Konstruktors der Dialogklasse auch Daten zwischen verschiedenen Dialogen übergeben.

```cs
[Serializable]
public class AgeDialog : IDialog<int>
{
    private string name;

    public AgeDialog(string name)
    {
        this.name = name;
    }
}
 ```

Aufrufen des Dialogcodes und Übergeben des Namens vom Benutzer.

```cs
private async Task NameDialogResumeAfter(IDialogContext context, IAwaitable<string> result)
{
    try
    {
        this.name = await result;
        context.Call(new AgeDialog(this.name), this.AgeDialogResumeAfter);
    }
    catch (TooManyAttemptsException)
    {
        await context.PostAsync("I'm sorry, I'm having issues understanding you. Let's try again.");
        await this.SendWelcomeMessageAsync(context);
    }
}
```

## <a name="sample-code"></a>Beispielcode 

Ein vollständiges Beispiel, das das Verwalten einer Konversation mithilfe von Dialogen im Bot Framework SDK für .NET zeigt, finden Sie auf GitHub im [BasicMultiDialog-Beispiel](https://aka.ms/v3cs-MultiDialog-Sample).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Dialoge](bot-builder-dotnet-dialogs.md)
- [Entwerfen und Steuern des Unterhaltungsflusses](../bot-service-design-conversation-flow.md)
- [Basic Multi-Dialog sample (GitHub)](https://aka.ms/v3cs-MultiDialog-Sample) (Einfaches Beispiel mit mehreren Dialogen)
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referenz zum Bot Framework SDK für .NET</a>

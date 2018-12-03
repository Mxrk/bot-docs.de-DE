---
title: Senden von proaktiven Nachrichten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe des Bot Builder SDK für .NET proaktive Nachrichten senden.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 201f1aa1aca0d75190335fa114ef8a26caed2a03
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49998339"
---
# <a name="send-proactive-messages"></a>Senden von proaktiven Nachrichten

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-proactive-messages.md)
> - [Node.js](../nodejs/bot-builder-nodejs-proactive-messages.md)

[!INCLUDE [Introduction to proactive messages - part 1](../includes/snippet-proactive-messages-intro-1.md)]

## <a name="types-of-proactive-messages"></a>Typen von proaktiven Nachrichten 

[!INCLUDE [Introduction to proactive messages - part 2](../includes/snippet-proactive-messages-intro-2.md)]

## <a name="send-an-ad-hoc-proactive-message"></a>Senden einer proaktiven Ad-hoc-Nachricht

Die folgenden Codebeispiele zeigen, wie Sie mit dem Bot Builder SDK für .NET eine proaktive Ad-hoc-Nachricht senden.

Um eine Ad-hoc-Nachricht an einen Benutzer senden zu können, muss der Bot zuerst aus der aktuellen Unterhaltung Informationen zum Benutzer sammeln und speichern. 

```cs
public virtual async Task MessageReceivedAsync(IDialogContext context, IAwaitable<IMessageActivity> result)
{
    var message = await result;
    
    // Extract data from the user's message that the bot will need later to send an ad hoc message to the user. 
    // Store the extracted data in a custom class "ConversationStarter" (not shown here).

    ConversationStarter.toId = message.From.Id;
    ConversationStarter.toName = message.From.Name;
    ConversationStarter.fromId = message.Recipient.Id;
    ConversationStarter.fromName = message.Recipient.Name;
    ConversationStarter.serviceUrl = message.ServiceUrl;
    ConversationStarter.channelId = message.ChannelId;
    ConversationStarter.conversationId = message.Conversation.Id;

    // (Save this information somewhere that it can be accessed later, such as in a database.)

    await context.PostAsync("Hello user, good to meet you! I now know your address and can send you notifications in the future.");
    context.Wait(MessageReceivedAsync);
}
```
> [!NOTE]
> Der Einfachheit halber wird in diesem Beispiel nicht angegeben, wie die Benutzerdaten gespeichert werden. Es spielt keine Rolle, wie die Daten gespeichert werden, solange der Bot sie später abrufen kann.

Da die Daten nun gespeichert wurden, kann der Bot einfach die Daten abzurufen und proaktive Ad-hoc-Nachricht erstellen und senden. 

```cs
// Use the data stored previously to create the required objects.
var userAccount = new ChannelAccount(toId,toName);
var botAccount = new ChannelAccount(fromId, fromName);
var connector = new ConnectorClient(new Uri(serviceUrl));

// Create a new message.
IMessageActivity message = Activity.CreateMessageActivity();
if (!string.IsNullOrEmpty(conversationId) && !string.IsNullOrEmpty(channelId))  
{
    // If conversation ID and channel ID was stored previously, use it.
    message.ChannelId = channelId;
}
else
{
    // Conversation ID was not stored previously, so create a conversation. 
    // Note: If the user has an existing conversation in a channel, this will likely create a new conversation window.
    conversationId = (await connector.Conversations.CreateDirectConversationAsync( botAccount, userAccount)).Id;
}

// Set the address-related properties in the message and send the message.
message.From = botAccount;
message.Recipient = userAccount;
message.Conversation = new ConversationAccount(id: conversationId);
message.Text = "Hello, this is a notification";
message.Locale = "en-us";
await connector.Conversations.SendToConversationAsync((Activity)message);
```

> [!NOTE]
> Wenn der Bot eine zuvor gespeicherte Konversations-ID angibt, wird die Nachricht wahrscheinlich in dem auf dem Client vorhandenen Unterhaltungsfenster an den Benutzer gesendet. Wenn der Bot eine neue Konversations-ID generiert, wird die Nachricht in einem neuen Unterhaltungsfenster auf dem Client an den Benutzer gesendet, sofern der Client mehrere Unterhaltungsfenster unterstützt. 

## <a name="send-a-dialog-based-proactive-message"></a>Senden einer dialogbasierten proaktiven Nachricht

Die folgenden Codebeispiele zeigen, wie Sie mit dem Bot Builder SDK für .NET eine dialogbasierte proaktive Nachricht senden.

Um eine dialogbasierte proaktive Nachricht an einen Benutzer senden zu können, muss der Bot zuerst aus der aktuellen Unterhaltung Informationen sammeln und speichern. 

```cs
public virtual async Task MessageReceivedAsync(IDialogContext context, IAwaitable<IMessageActivity> result)
{
    var message = await result;
    
    // Store information about this specific point the conversation, so that the bot can resume this conversation later.
    var conversationReference = message.ToConversationReference();
    ConversationStarter.conversationReference = JsonConvert.SerializeObject(conversationReference);

    await context.PostAsync("Greetings, user! I now know how to start a proactive message to you."); 
    context.Wait(MessageReceivedAsync);
}
```

Wenn die Zeit zum Senden der Nachricht gekommen ist, erstellt der Bot einen neuen Dialog und fügt ihn am oberen Ende des Dialogstapels hinzu. Der neue Dialog übernimmt die Steuerung der Unterhaltung, übermittelt die proaktive Nachricht, wird geschlossen, und die Steuerung wird wieder an den vorherigen Dialog im Stapel übergeben. 

```cs
// This will interrupt the conversation and send the user to SurveyDialog, then wait until that's done 
public static async Task Resume() 
{
    // Recreate the message from the conversation reference that was saved previously.
    var message = JsonConvert.DeserializeObject<ConversationReference>(conversationReference).GetPostToBotMessage(); 
    var client = new ConnectorClient(new Uri(message.ServiceUrl));

    // Create a scope that can be used to work with state from bot framework.
    using (var scope = DialogModule.BeginLifetimeScope(Conversation.Container, message))
    {
        var botData = scope.Resolve<IBotData>();
        await botData.LoadAsync(CancellationToken.None);

        // This is our dialog stack.
        var task = scope.Resolve<IDialogTask>();

        // Create the new dialog and add it to the stack.
        var dialog = new SurveyDialog();
        // interrupt the stack. This means that we're stopping whatever conversation that is currently happening with the user
        // Then adding this stack to run and once it's finished, we will be back to the original conversation
        task.Call(dialog.Void<object, IMessageActivity>(), null);
        
        await task.PollAsync(CancellationToken.None);

        // Flush the dialog stack back to its state store.
        await botData.FlushAsync(CancellationToken.None);        
    }
}
```
Der `SurveyDialog` steuert die Unterhaltung bis zum Ende. Wenn die Aufgabe abgeschlossen ist, wird `context.Done` aufgerufen, der Dialog geschlossen und die Steuerung an den vorherigen Dialog zurückgegeben. 

```cs
[Serializable]
public class SurveyDialog : IDialog<object>
{
    public async Task StartAsync(IDialogContext context)
    {
        await context.PostAsync("Hello, I'm the survey dialog. I'm interrupting your conversation to ask you a question. Type \"done\" to resume");

        context.Wait(this.MessageReceivedAsync);
    }
    public virtual async Task MessageReceivedAsync(IDialogContext context, IAwaitable<IMessageActivity> result)
    {
        if ((await result).Text == "done")
        {
            await context.PostAsync("Great, back to the original conversation!");
            context.Done(String.Empty); // Finish this dialog.
        }
        else
        {
            await context.PostAsync("I'm still on the survey until you type \"done\"");
            context.Wait(MessageReceivedAsync); // Not done yet.
        }
    }
}
```

## <a name="sample-code"></a>Beispielcode

Ein vollständiges Beispiel, das zeigt, wie Sie proaktive Nachrichten mit dem Bot Builder SDK für .NET senden können, finden Sie in GitHub im <a href="https://aka.ms/proactive-messaging-cs-v3 " target="_blank">Beispiel für proaktive Nachrichten</a>. Im Beispiel für proaktive Nachrichten zeigt <a href="https://aka.ms/proactive-sendmessage-cs-v3 " target="_blank">simpleSendMessage</a>, wie eine proaktive Ad-hoc-Nachricht gesendet wird, und <a href="https://aka.ms/proactive-newdialog-cs-v3 " target="_blank">startNewDialog</a> zeigt, wie Sie eine dialogbasierte proaktive Nachricht senden. 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Entwerfen und Steuern des Unterhaltungsflusses](../bot-service-design-conversation-flow.md)
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referenz zum Bot Builder SDK für .NET</a>
- <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-proactiveMessages" target="_blank">Beispiel für proaktive Nachrichten (GitHub)</a>


---
title: Globale Meldungshandler mit bewertbaren Dialogen
description: Erstellen Sie flexiblere, bewertbare Dialoge mit dem Bot Builder SDK für .NET.
author: matthewshim-ms
ms.author: v-shimma
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 8ba63ad99c772c7cf5884180a62244e0dfe11db2
ms.sourcegitcommit: 67445b42796d90661afc643c6bb6533e9a662cbc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39574906"
---
# <a name="global-message-handlers-using-scorables"></a>Globale Meldungshandler mit bewertbaren Dialogen

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Benutzer versuchen in der Mitte einer Konversation mithilfe von Begriffen wie „Hilfe“, „Abbrechen“ oder „Neustart“ bestimmte Funktionen in einem Bot aufzurufen, wenn der Bot eine andere Antwort erwartet. Sie können solche Anforderungen in Ihrem Bot sauber verarbeiten, indem Sie bewertbare Dialoge verwenden.

Bewertbare Dialoge überwachen alle eingehenden Nachrichten und legen fest, ob auf eine Nachricht irgendeine Art von Aktion erfolgen muss. Bewertbaren Nachrichten wird von jedem bewertbaren Dialog eine Bewertung zwischen 0 und 1 zugewiesen. Der bewertbare Dialog, der die höchste Bewertung festlegt, wird an oberster Stelle des Dialogstapels hinzugefügt und übergibt dann die Antwort an den Benutzer. Nachdem der bewertbare Dialog die Ausführung abgeschlossen hat, wird die Konversation vom Punkt der Unterbrechung an fortgesetzt.

Bewertbare Dialoge ermöglichen das Erstellen flexiblerer Konversationen, indem Sie den Benutzern erlauben, den normalen Konversationsfluss in normalen Dialogen zu unterbrechen.

## <a name="create-a-scorable-dialog"></a>Erstellen eines bewertbaren Dialogs

Definieren Sie zuerst einen neuen [Dialog](bot-builder-dotnet-dialogs.md). Der folgende Code verwendet einen Dialog, der von der `IDialog`-Schnittstelle abgeleitet ist.

```cs
public class SampleDialog : IDialog<object>
{
    public async Task StartAsync(IDialogContext context)
    {
        await context.PostAsync("This is a Sample Dialog which is Scorable. Reply with anything to return to the prior prior dialog.");

        context.Wait(this.MessageReceived);
    }

    private async Task MessageReceived(IDialogContext context, IAwaitable<IMessageActivity> result)
    {
        var message = await result;

        if ((message.Text != null) && (message.Text.Trim().Length > 0))
        {
            context.Done<object>(null);
        }
        else
        {
            context.Fail(new Exception("Message was not a string or was an empty string."));
        }
    }
}
```
Um einen Dialog bewertbar zu machen, erstellen Sie eine Klasse, die von der abstrakten `ScorableBase`-Klasse erbt. Der folgende Code zeigt eine `SampleScorable`-Klasse.

```cs
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Builder.Dialogs.Internals;
using Microsoft.Bot.Builder.Internals.Fibers;
using Microsoft.Bot.Builder.Scorables.Internals;

public class SampleScorable : ScorableBase<IActivity, string, double>
{
    private readonly IDialogTask task;

    public SampleScorable(IDialogTask task)
    {
        SetField.NotNull(out this.task, nameof(task), task);
    }
}
```
Die abstrakte `ScorableBase`-Klasse erbt von der `IScorable`-Schnittstelle. Sie müssen die folgenden `IScorable`-Methoden in der Klasse implementieren:

- `PrepareAsync` ist die erste Methode, die in der bewertbaren Instanz aufgerufen wird. Sie akzeptiert die eingehende Nachrichtenaktivität, analysiert den Dialog und legt seinen Zustand fest. Dieser wird an alle anderen Methoden der `IScorable`-Schnittstelle übergeben.

```cs
protected override async Task<string> PrepareAsync(IActivity item, CancellationToken token)
{
        // TODO: insert your code here
}
```

- Die `HasScore`-Methode überprüft die Zustandseigenschaft, um zu ermitteln, ob der bewertbare Dialog eine Bewertung für die Nachricht bereitstellen sollte. Bei der Rückgabe von FALSE wird die Nachricht vom bewertbaren Dialog ignoriert.

```cs
protected override bool HasScore(IActivity item, string state)
{
        // TODO: insert your code here
}
```

- `GetScore` wird nur ausgelöst, wenn `HasScore` TRUE zurückgibt. Sie stellen die Logik in dieser Methode bereit, die die Bewertung einer Nachricht zwischen 0 und 1 festlegt.

```cs
protected override double GetScore(IActivity item, string state)
{
        // TODO: insert your code here
}
```
- Definieren Sie in der `PostAsync`-Methode Kernaktionen, die für die bewertbare Klasse ausgeführt werden sollen. Alle bewertbaren Dialoge überwachen eingehende Nachrichten und weisen gültigen Nachrichten anhand der GetScore-Methode von bewertbaren Dialogen Bewertungen zu. Die bewertbare Klasse, die die höchste Bewertung festlegt (zwischen 0 und 1,0) löst dann die `PostAsync`-Methode dieses bewertbaren Dialogs aus.

```cs
protected override Task PostAsync(IActivity item, string state, CancellationToken token)
{
        //TODO: insert your code here
}
```

- `DoneAsync` wird nach dem Abschluss der Bewertung aufgerufen. Verwenden Sie diese Methode, um Ressourcen in diesem Bereich auszuschließen.

```cs
protected override Task DoneAsync(IActivity item, string state, CancellationToken token)
{
        //TODO: insert your code here
}
```

## <a name="create-a-module-to-register-the-iscorable-service"></a>Erstellen eines Moduls zum Registrieren des IScorable-Diensts

Definieren Sie als Nächstes ein `Module`, das die `SampleScorable`-Klasse als Komponente registriert. Hierbei wird der `IScorable`-Dienst bereitgestellt.

```cs
public class GlobalMessageHandlersBotModule : Module
{
    protected override void Load(ContainerBuilder builder)
    {
        base.Load(builder);

        builder
            .Register(c => new SampleScorable(c.Resolve<IDialogTask>()))
            .As<IScorable<IActivity, double>>()
            .InstancePerLifetimeScope();
    }
}
```
## <a name="register-the-module"></a>Registrieren des Moduls  

Der letzte Schritt im Prozess besteht darin, `SampleScorable` auf den Konversationscontainer des Bots anzuwenden. Dadurch wird der bewertbare Dienst in der Meldungsbehandlungs-Pipeline von Bot Framework registriert. Der folgende Code zeigt die Aktualisierung von `Conversation.Container` in der Initialisierung der Bot-App in **Global.asax.cs**:

```cs
public class WebApiApplication : System.Web.HttpApplication
{
    protected void Application_Start()
    {
        this.RegisterBotModules();
        GlobalConfiguration.Configure(WebApiConfig.Register);
    }

    private void RegisterBotModules()
    {
        var builder = new ContainerBuilder();
        builder.RegisterModule(new ReflectionSurrogateModule());

        //Register the module within the Conversation container
        builder.RegisterModule<GlobalMessageHandlersBotModule>();

        builder.Update(Conversation.Container);
    }
}
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen
* [Globale Nachrichtenhandler – Beispiel](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-GlobalMessageHandlers)
* [Beispiel für einfachen bewertbaren Bot](https://github.com/Microsoft/BotFramework-Samples/tree/master/blog-samples/CSharp/ScorableBotSample)
* [Übersicht über Dialoge](bot-builder-dotnet-dialogs.md)
* [AutoFac](https://autofac.org/)

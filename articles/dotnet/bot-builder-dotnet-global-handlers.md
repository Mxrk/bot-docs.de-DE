---
title: Implementieren von globalen Nachrichtenhandlern | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Ihrem Bot ermöglichen, mit dem Bot Builder SDK für .NET auf Benutzereingaben mit bestimmten Schlüsselwörtern zu achten und diese zu verarbeiten.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 796e36fdfd5ef2ee20ba1f5e000f92ef3e4ec1ae
ms.sourcegitcommit: 67445b42796d90661afc643c6bb6533e9a662cbc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39574656"
---
# <a name="implement-global-message-handlers"></a>Implementieren von globalen Nachrichtenhandlern

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

[!INCLUDE [Introduction to global message handlers](../includes/snippet-global-handlers-intro.md)]

## <a name="listen-for-keywords-in-user-input"></a>Erkennen von Schlüsselwörtern in der Benutzereingabe

Die folgende Anleitung zeigt, wie Sie mit dem Bot Builder SDK für .NET globale Nachrichtenhandler implementieren.

Zuerst registriert `Global.asax.cs` `GlobalMessageHandlersBotModule`, dessen Implementierung wie hier gezeigt erfolgt. In diesem Beispiel registriert das Modul zwei bewertbare Elemente: eins für die Verwaltung einer Anforderung zum Ändern von Einstellungen (`SettingsScorable`) und eins für die Verwaltung einer Anforderung zum Abbrechen (`CancelScoreable`).

```cs
public class GlobalMessageHandlersBotModule : Module
{
    protected override void Load(ContainerBuilder builder)
    {
        base.Load(builder);

        builder
            .Register(c => new SettingsScorable(c.Resolve<IDialogTask>()))
            .As<IScorable<IActivity, double>>()
            .InstancePerLifetimeScope();

        builder
            .Register(c => new CancelScorable(c.Resolve<IDialogTask>()))
            .As<IScorable<IActivity, double>>()
            .InstancePerLifetimeScope();
    }
}
```

`CancelScorable` enthält eine `PrepareAsync`-Methode, die den Trigger definiert: Wenn der Nachrichtentext „abbrechen“ lautet, wird dieses bewertbare Element ausgelöst.

```cs
protected override async Task<string> PrepareAsync(IActivity activity, CancellationToken token)
{
    var message = activity as IMessageActivity;
    if (message != null && !string.IsNullOrWhiteSpace(message.Text))
    {
        if (message.Text.Equals("cancel", StringComparison.InvariantCultureIgnoreCase))
        {
            return message.Text;
        }
    }
    return null;
}
```

Wenn eine „cancel“-Anforderung (Abbrechen) empfangen wird, setzt die `PostAsync`-Methode in `CancelScoreable` den Dialogstapel zurück. 

```cs
protected override async Task PostAsync(IActivity item, string state, CancellationToken token)
{
    this.task.Reset();
}
```

Wenn eine „change settings“-Anforderung (Einstellungen ändern) empfangen wird, ruft die `PostAsync`-Methode in `SettingsScorable` die `SettingsDialog`-Methode auf, d.h. die Anforderung wird an den Dialog übergeben. `SettingsDialog` wird dabei dem Dialogstapel hinzugefügt und steuert fortan die Unterhaltung.

```cs
protected override async Task PostAsync(IActivity item, string state, CancellationToken token)
{
    var message = item as IMessageActivity;
    if (message != null)
    {
        var settingsDialog = new SettingsDialog();
        var interruption = settingsDialog.Void<object, IMessageActivity>();
        this.task.Call(interruption, null);
        await this.task.PollAsync(token);
    }
}
```

## <a name="sample-code"></a>Beispielcode

Ein vollständiges Beispiel für das Implementieren eines globalen Nachrichtenhandlers mit dem Bot Builder SDK für .NET finden Sie in GitHub unter <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-GlobalMessageHandlers" target="_blank">Globale Nachrichtenhandler – Beispiel</a>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Entwerfen und Steuern des Unterhaltungsflusses](../bot-service-design-conversation-flow.md)
- <a href="/dotnet/api/?view=botbuilder-3.12.2.4" target="_blank">Referenz zum Bot Builder SDK für .NET</a>
- <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-GlobalMessageHandlers" target="_blank">Globale Nachrichtenhandler – Beispiel</a> (GitHub)

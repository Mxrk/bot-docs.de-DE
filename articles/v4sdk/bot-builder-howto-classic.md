---
title: Ausführen eines .NET SDK V3-Bots in SDK V4 | Microsoft-Dokumentation
description: Informationen zum Konvertieren eines Bots von Version 3.x zu Version 4.0 mithilfe des klassischen NuGet-Pakets
keywords: migration, klassischer bot, konvertieren v3, v3 auf v4
author: v-royhar
ms.author: v-royhar
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 4/25/18
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: a808076e4865a181802b85cfc24ce342dbf23cba
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301490"
---
# <a name="how-to-run-net-sdk-v3-bots-in-sdk-40"></a>Ausführen eines .NET SDK V3-Bots im SDK 4.0

Das NuGet-Paket **Microsoft.Bot.Builder.Classic** erleichtert die Migration Ihrer Bots von Version 3.x auf Version 4.0 von Microsoft Bot Framework.

**HINWEIS:** Dieser Prozess funktioniert nur für Bots der **ASP.NET-Webanwendung (.NET Framework)**. Bei Bots der **ASP.NET Core-Webanwendung** funktioniert der Prozess nicht.

## <a name="the-process"></a>Der Prozess

Dieser Prozess ist relativ einfach:

- Fügen Sie dem Projekt das NuGet-Paket **Microsoft.Bot.Builder.Classic** hinzu.
    - Möglicherweise müssen Sie auch das NuGet-Paket **Autofac** aktualisieren.
- Aktualisieren Sie die Namespaces **Microsoft.Bot.Builder.Classic**.
- Rufen Sie mithilfe von **ITurnContext** der Version 4.0 basierend auf **Conversation.SendAsync()** Dialogfelder auf.

### <a name="add-the-microsoftbotbuilderclassic-nuget-package"></a>Hinzufügen des NuGet-Pakets „Microsoft.Bot.Builder.Classic“

Verwenden Sie die Option **NuGet-Pakete verwalten**, um das NuGet-Paket **Microsoft.Bot.Builder.Classic** hinzuzufügen.

### <a name="update-the-namespaces"></a>Aktualisieren der Namespaces

Löschen Sie eine der folgenden `using`-Anweisungen, um die Namespaces zu aktualisieren:

```csharp
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Builder.Dialogs.Internals;
using Microsoft.Bot.Builder.FormFlow;
using Microsoft.Bot.Builder.Scorables;
```

Fügen Sie dann stattdessen die folgenden `using`-Anweisungen hinzu:

```csharp
using Microsoft.Bot.Builder.Adapters;
using Microsoft.Bot.Builder.Classic.Dialogs;
using Microsoft.Bot.Builder.Classic.Dialogs.Internals;
using Microsoft.Bot.Builder.Classic.FormFlow;
using Microsoft.Bot.Builder.Classic.Scorables;
using Microsoft.Bot.Connector;
using Microsoft.Bot.Schema;
```

Wenn Sie über eine `[BotAuthentication]`-Zeile verfügen, löschen Sie diese, oder kommentieren Sie sie aus.

### <a name="invoke-your-3x-dialog"></a>Aufrufen Ihres 3.x-Dialogfelds

Verwenden Sie weiterhin `Conversation.SendAsync`, um Ihr 3.x-Dialogfeld aufzurufen. Allerdings wird jetzt ein **ITurnContext**-Objekt der Version 4.0 anstelle eines **Activity**-Objekts benötigt.

```csharp
// invoke a Classic V3 IDialog 
await Conversation.SendAsync(turnContext, () => new EchoDialog());
```

Wenn Sie zwar nicht über ein **ITurnContext**-Objekt, dafür aber über ein **Activity**-Objekt verfügen, können Sie ersteres wie folgt abrufen:

```csharp
BotFrameworkAdapter adapter = new BotFrameworkAdapter("", "");

await adapter.ProcessActivity(this.Request.Headers.Authorization?.Parameter,
        activity,
        async (context) =>
        {
            // Do something with context here. For example, the body of your Post() method may go here.
        });
```

## <a name="fix-assembly-conflicts"></a>Beheben von Assemblykonflikten

Bei Bots, die mit dem klassischen NuGet-Paket erstellt wurden, entstehen Konflikte mit den jeweiligen Assemblys. Dieser wird im Fenster „Fehlerliste“ in Visual Studio angezeigt, nachdem der Build erstellt wurde.

### <a name="if-you-see-warning-found-conflicts-between-different-versions-of-the-same-dependent-assembly"></a>Vorgehensweise bei der Warnung "Warning: Found conflicts between different versions of the same dependent assembly." (Konflikte zwischen verschiedenen Versionen der gleichen abhängigen Assembly wurden gefunden.)

Gehen Sie wie folgt vor, wenn die Warnung **Found conflicts between different versions of the same dependent assembly.** (Konflikte zwischen verschiedenen Versionen der gleichen abhängigen Assembly wurden gefunden.) angezeigt wird:

- Doppelklicken Sie auf die Warnmeldung. Dann wird ein Dialogfeld mit der folgenden Frage angezeigt: "Do you want to fix these conflicts by adding binding redirect records in the application configuration file?" (Möchten Sie diese Konflikte durch Hinzufügen bindender Umleitungsdatensätze in die Anwendungskonfigurationsdatei beheben?).
- Klicken Sie auf „Ja“.
- Erstellen Sie das Projekt erneut.

### <a name="if-you-see-error-missing-method-exception-on-startup"></a>Vorgehensweise bei dem Fehler "Error: Missing Method Exception on startup" (Fehler: Fehlende Methodenausnahme beim Start.)

Bei .NET Standard entsteht ein Fehler, wenn Sie ein älteres .NET 4.6-Projekt verwenden und ein Upgrade auf Version 4.6.1 ausführen und anschließend versuchen, eine .NET Standard-Bibliothek damit zu verwenden. Im Grunde genommen gibt es zwei verschiedene System.Net.Http-Assemblys, die versuchen, einen dynamischen Wechsel auszuführen. Sie können das Problem umgehen, indem Sie eine bindende Umleitung auf Ihre Web.config-Datei für System.Net.Http hinzufügen. 

Wenn Sie diesen Fehler erhalten, fügen Sie Folgendes zur Ihrer Web.config-Datei hinzu:

```xml
<dependentAssembly>
    <assemblyIdentity name="System.Net.Http" publicKeyToken="B03F5F7F11D50A3A" culture="neutral" />
    <bindingRedirect oldVersion="0.0.0.0-4.2.0.0" newVersion="4.2.0.0" />
</dependentAssembly>
```

Weitere Informationen zu diesem Problem finden Sie unter [System.Net.Http v4.2.0.0 being copied/loaded from MSBuild tooling #25773 (Kopieren/Laden von System.Net.Http v4.2.0.0 aus dem MSBuild-Tool #25773)](https://github.com/dotnet/corefx/issues/25773).

## <a name="sample-of-a-converted-bot"></a>Beispiel für einen konvertierten Bot

Für einen Bot, der bereits konvertiert wurde, können Sie das Beispiel [EchoBot-Classic](https://github.com/Microsoft/botbuilder-dotnet/tree/master/samples/Microsoft.Bot.Samples.EchoBot-Classic) ausprobieren, das zeigt, wie ein 3.x Bot so konvertiert wird, dass er mit 4.0 funktioniert.

## <a name="limitations"></a>Einschränkungen
Bei Microsoft.Bot.Builder.Classic handelt es sich nur um eine .NET 4.61-Bibliothek, die nicht in .NET Core funktioniert.

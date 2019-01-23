---
title: Abfangen von Nachrichten | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Nachrichten zwischen Benutzer und Bot mithilfe des Bot Framework SDK für .NET abfangen.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 54ab447fac81ba17f1fa277347cf7a5aaaab8850
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54223775"
---
# <a name="intercept-messages"></a>Abfangen von Nachrichten

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-middleware.md)
> - [Node.js](../nodejs/bot-builder-nodejs-intercept-messages.md)

[!INCLUDE [Introducton to message logging](../includes/snippet-message-logging-intro.md)]

## <a name="intercept-and-log-messages"></a>Abfangen und Protokollieren von Nachrichten

Das folgende Codebeispiel zeigt, wie zwischen Benutzer und Bot ausgetauschte Nachrichten mithilfe des Konzepts der **Middleware** im Bot Framework SDK für .NET abgefangen werden. 

Erstellen Sie zuerst eine `DebugActivityLogger`-Klasse, und definieren Sie eine `LogAsync`-Methode, um anzugeben, welche Aktion für jede abgefangene Nachricht ausgeführt wird. In diesem Beispiel werden nur einige Informationen zu jeder Nachricht ausgegeben.

```cs
public class DebugActivityLogger : IActivityLogger
{
    public async Task LogAsync(IActivity activity)
    {
        Debug.WriteLine($"From:{activity.From.Id} - To:{activity.Recipient.Id} - Message:{activity.AsMessageActivity()?.Text}");
    }
}
```

Fügen Sie anschließend den folgenden Code zu `Global.asax.cs`hinzu.  Alle zwischen Benutzer und Bot (in beide Richtungen) ausgetauschten Nachrichten lösen jetzt die `LogAsync`-Methode in der `DebugActivityLogger`-Klasse aus. 

```cs
    public class WebApiApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
            var builder = new ContainerBuilder();
            builder.RegisterType<DebugActivityLogger>().AsImplementedInterfaces().InstancePerDependency();
            builder.Update(Conversation.Container);

            GlobalConfiguration.Configure(WebApiConfig.Register);
        }
    }
```

In diesem Beispiel werden einfach einige Informationen zu jeder Nachricht ausgegeben. Sie können jedoch die `LogAsync`-Methode aktualisieren, um die Aktionen anzugeben, die für jede Nachricht ausgeführt werden sollen. 

## <a name="sample-code"></a>Beispielcode 

Ein vollständiges Beispiel, das veranschaulicht, wie Sie Nachrichten mit dem Bot Framework SDK für .NET abfangen und protokollieren können, finden Sie auf GitHub im <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-Middleware" target="_blank">Middleware-Beispiel</a>. 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referenz zum Bot Framework SDK für .NET</a>
- <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-Middleware" target="_blank">Middleware-Beispiel (GitHub)</a>

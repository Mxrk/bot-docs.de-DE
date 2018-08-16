---
title: Absichern eines Bots | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihren Bot mithilfe von HTTPS und der Bot Framework-Authentifizierung absichern.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/17
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 331effc0bf604388995288e5d7c3ca9d54537f94
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301450"
---
# <a name="secure-your-bot"></a>Absichern eines Bots

Sie können Ihren Bot mit vielen verschiedenen Kommunikationskanälen (beispielsweise Skype, SMS und E-Mail) über den Bot Framework Connector-Dienst verbinden. In diesem Artikel wird beschrieben, wie Sie Ihren Bot mithilfe von HTTPS und der Bot Framework-Authentifizierung absichern.

## <a name="use-https-and-bot-framework-authentication"></a>Verwenden von HTTPS und der Bot Framework-Authentifizierung

Wenn Sie sicherstellen möchten, dass der Endpunkt Ihres Bots nur über den [Bot Framework-Connector](bot-builder-dotnet-concepts.md#connector) erreichbar ist, müssen Sie den Endpunkt so konfigurieren, dass dieser nur HTTPS verwendet. Zusätzlich müssen Sie die Bot Framework-Authentifizierung aktivieren, indem Sie den Bot [registrieren](~/bot-service-quickstart-registration.md). Dadurch werden die zugehörigen Werte für die App-ID und das Kennwort abgerufen. 

## <a name="configure-authentication-for-your-bot"></a>Konfigurieren der Botauthentifizierung

Legen Sie für Ihren Bot die App-ID und das Kennwort in der Datei „web.config“ fest. 

> [!NOTE]
> Unter [MicrosoftAppID and MicrosoftAppPassword (MicrosoftAppID und MicrosoftAppPassword)](~/bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword) wird beschrieben, wie Sie die Werte für **MicrosoftAppId** und **MicrosoftAppPassword** finden.

```xml
<appSettings>
    <add key="MicrosoftAppId" value="_appIdValue_" />
    <add key="MicrosoftAppPassword" value="_passwordValue_" />
</appSettings>
```

Legen Sie anschließend mithilfe des `[BotAuthentication]`-Attributs die Anmeldeinformationen fest, die beim Erstellen eines Bots mit dem Bot Builder SDK für .NET verwendet werden sollen. 

Wenn Sie die Anmeldeinformationen in der Datei „web.config“ nutzen möchten, müssen Sie `[BotAuthentication]` ohne Parameter verwenden.

[!code-csharp[Use botAuthentication attribute](../includes/code/dotnet-security.cs#attribute1)]

Andere Anmeldeinformationen können Sie festlegen, indem Sie das `[BotAuthentication]`-Attribut zusammen mit den gewünschten Werten angeben.

[!code-csharp[Use botAuthentication attribute with parameters](../includes/code/dotnet-security.cs#attribute2)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Bot Builder SDK for .NET (Bot Builder SDK für .NET)](bot-builder-dotnet-overview.md)
- [Key concepts in the bot Builder SDK for .NET (Schlüsselbegriffe für das Bot Builder SDK für .NET)](bot-builder-dotnet-concepts.md)
- [Register a bot with the Bot Framework (Registrieren eines Bots bei Bot Framework)](~/bot-service-quickstart-registration.md)
---
title: Hinzufügen von Medienanlagen zu Nachrichten | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mit dem Bot Framework SDK für .NET Nachrichten Medienanlagen hinzufügen.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 6dcfe6595f1c5961151a90783dd8ceee9c7684dd
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54224335"
---
# <a name="add-media-attachments-to-messages"></a>Hinzufügen von Medienanlagen zu Nachrichten

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-media-attachments.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-receive-attachments.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-media-attachments.md)

Ein Nachrichtenaustausch zwischen Benutzer und Bot kann Medienanlagen enthalten, z.B. Bilder, Videos, Audio oder Dateien. Die `Attachments`-Eigenschaft des <a href="https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Activity</a>-Objekts enthält ein Array von <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.connector.attachments?view=botconnector-3.12.2.4" target="_blank">Attachment</a>-Objekten, die die an die Nachricht angehängten Medienanlagen und Rich Cards darstellen. 

> [!NOTE]
> [Hinzufügen von Rich Cards zu Nachrichten](bot-builder-dotnet-add-rich-card-attachments.md)

## <a name="add-a-media-attachment"></a>Hinzufügen einer Medienanlage  

Um einer Nachricht eine Medienanlage hinzuzufügen, erstellen Sie ein `Attachment`-Objekt für die Aktivität `message`, und legen Sie die Eigenschaften `ContentType`, `ContentUrl` und `Name` fest. 

[!code-csharp[Add media attachment](../includes/code/dotnet-add-attachments.cs#addMediaAttachment)]

Wenn eine Anlage ein Bild, eine Audiodatei oder ein Video ist, übermittelt der Connector Anlagedaten an den Kanal so, dass der [Kanal](bot-builder-dotnet-channeldata.md) diese Anlage in der Unterhaltung rendert. Wenn es sich bei der Anlage um eine Datei handelt, wird die Datei-URL als Link in der Unterhaltung gerendert.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Funktionsvorschau mit Channel Inspector][inspector]
- [Übersicht über Aktivitäten](bot-builder-dotnet-activities.md)
- [Erstellen von Nachrichten](bot-builder-dotnet-create-messages.md)
- [Hinzufügen von Rich Cards zu Nachrichten](bot-builder-dotnet-add-rich-card-attachments.md)
- <a href="https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Activity-Klasse</a>
- <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.connector.attachments?view=botconnector-3.12.2.4" target="_blank">Attachment-Klasse</a>

[inspector]: ../bot-service-channel-inspector.md



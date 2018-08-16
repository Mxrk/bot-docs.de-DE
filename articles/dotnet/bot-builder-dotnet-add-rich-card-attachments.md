---
title: Hinzufügen von Rich Card-Anlagen zu Nachrichten | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mit dem Bot Builder SDK für .NET Nachrichten Rich Cards hinzufügen.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 4552fd8a38468b000837ef0f580d3a0e504a882b
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39302584"
---
# <a name="add-rich-card-attachments-to-messages"></a>Hinzufügen von Rich Card-Anlagen zu Nachrichten
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-rich-card-attachments.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-rich-cards.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-rich-cards.md)

Ein Nachrichtenaustausch zwischen Benutzer und Bot kann eine oder mehrere Rich Cards enthalten, die als Liste oder Karussell dargestellt werden. Die `Attachments`-Eigenschaft des <a href="https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Activity</a>-Objekts enthält ein Array von <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.connector.attachments?view=botconnector-3.12.2.4" target="_blank">Attachment</a>-Objekten, die die an die Nachricht angehängten Medienanlagen und Rich Cards darstellen. 

> [!NOTE]
> Weitere Informationen finden Sie unter [Hinzufügen von Medienanlagen zu Nachrichten](bot-builder-dotnet-add-media-attachments.md).

## <a name="types-of-rich-cards"></a>Typen von Rich Cards

Bot Framework unterstützt derzeit acht Typen von Rich Cards: 

| Kartentyp | BESCHREIBUNG |
|----|----|
| <a href="/adaptive-cards/get-started/bots">Adaptive Karten</a> | Anpassbare Rich Cards, die eine beliebige Kombination von Text, Sprache, Bildern, Schaltflächen und Eingabefeldern enthalten können. Weitere Informationen finden Sie unter [Unterstützung pro Kanal](/adaptive-cards/get-started/bots#channel-status).  |
| [Animationskarten][animationCard] | Rich Cards, die animierte GIF-Dateien oder kurze Videos abspielen können. |
| [Audiokarten][audioCard] | Rich Cards, die eine Audiodatei abspielen können. |
| [Hero Cards][heroCard] | Rich Cards, die in der Regel ein einzelnes großes Bild, eine oder mehrere Schaltflächen sowie Text enthalten. |
| [Miniaturbildkarten][thumbnailCard] | Rich Cards, die in der Regel ein Miniaturbild, eine oder mehrere Schaltflächen sowie Text enthalten. |
| [Quittungskarten][receiptCard] | Rich Cards, mit denen ein Bot Benutzern eine Quittung bereitstellen kann. Sie enthalten in der Regel die Liste der Elemente, die in der Quittung enthalten sein sollen, Steuer- und Gesamtbeträge sowie anderen Text. |
| [Anmeldekarten][signinCard] | Rich Cards, mit denen ein Bot die Anmeldung eines Benutzers anfordern kann. Sie enthalten in der Regel Text und eine oder mehrere Schaltflächen, auf die der Benutzer klicken kann, um den Anmeldevorgang zu initiieren. |
| [Videokarte][videoCard] | Rich Cards, die Videos abspielen können. |

> [!TIP]
> Um mehrere Rich Cards im Listenformat anzuzeigen, legen Sie die `AttachmentLayout`-Eigenschaft der Aktivität auf „list“ fest. Um mehrere Rich Cards im Karussellformat anzuzeigen, legen Sie die `AttachmentLayout`-Eigenschaft der Aktivität auf „carousel“ fest. Wenn der Kanal das Karussellformat nicht unterstützt, werden die Rich Cards im Listenformat angezeigt, auch wenn die `AttachmentLayout`-Eigenschaft auf „carousel“ festgelegt ist.

## <a name="process-events-within-rich-cards"></a>Verarbeiten von Ereignissen auf Rich Cards

Zum Verarbeiten von Ereignissen auf Rich Cards verwenden Sie `CardAction`-Objekte, um festzulegen, was passieren soll, wenn der Benutzer auf eine Schaltfläche klickt oder auf einen Abschnitt der Rich Card tippt. Jedes `CardAction`-Objekt enthält die folgenden Eigenschaften:

| Eigenschaft | Typ | BESCHREIBUNG | 
|----|----|----|
| Typ | Zeichenfolge | Typ der Aktion (einer der in der folgenden Tabelle angegebenen Werte) |
| Titel | Zeichenfolge | Titel der Schaltfläche |
| Image | Zeichenfolge | Bild-URL für die Schaltfläche |
| Wert | Zeichenfolge | Wert, der zum Ausführen des angegebenen Aktionstyps erforderlich ist |

> [!NOTE]
> Schaltflächen auf adaptiven Karten werden nicht mit `CardAction`-Objekten, sondern mit dem von <a href="http://adaptivecards.io" target="_blank">adaptiven Karten</a> definierten Schema erstellt. Ein Beispiel, das zeigt, wie Sie einer adaptiven Karte Schaltflächen hinzufügen, finden Sie unter [Hinzufügen einer adaptiven Karte zu Nachrichten](#adaptive-card).

Diese Tabelle listet die gültigen Werte für `CardAction.Type` auf und beschreibt den erwarteten Inhalt von `CardAction.Value` für jeden Typ:

| CardAction.Type | CardAction.Value | 
|----|----|
| openUrl | Die URL, die im integrierten Browser geöffnet werden soll. |
| imBack | Der Text der Nachricht, die an den Bot gesendet werden soll (vom Benutzer, der auf die Schaltfläche geklickt oder auf die Karte getippt hat). Diese Nachricht (vom Benutzer an den Bot) ist für alle Unterhaltungsteilnehmer über die Clientanwendung sichtbar, auf der die Unterhaltung gehostet wird. |
| postBack | Der Text der Nachricht, die an den Bot gesendet werden soll (vom Benutzer, der auf die Schaltfläche geklickt oder auf die Karte getippt hat). Einige Clientanwendungen zeigen diesen Text ggf. im Nachrichtenfeed an, wo er für alle Teilnehmer der Unterhaltung sichtbar ist. |
| Aufruf | Ziel für einen Telefonanruf, im folgenden Format: **tel:123123123123**. |
| playAudio | Die URL der Audiodatei, die wiedergegeben werden soll. |
| playVideo | Die URL des Videos, das wiedergegeben werden soll. |
| showImage | Die URL des Bilds, das angezeigt werden soll. |
| downloadFile | Die URL der Datei, die heruntergeladen werden soll. |
| signin | Die URL des OAuth-Flusses, der initiiert werden soll. |

## <a name="add-a-hero-card-to-a-message"></a>Hinzufügen einer Hero Card zu einer Nachricht

Hero Cards enthalten in der Regel ein einzelnes großes Bild, eine oder mehrere Schaltflächen und Text. 

Dieses Codebeispiel zeigt, wie Sie eine Antwortnachricht erstellen, die drei Hero Cards im Karussellformat enthält: 

[!code-csharp[Add HeroCard attachment](../includes/code/dotnet-add-attachments.cs#addHeroCardAttachment)]

## <a name="add-a-thumbnail-card-to-a-message"></a>Hinzufügen einer Miniaturbildkarte zu einer Nachricht

Miniaturbildkarten enthalten in der Regel ein Miniaturbild, eine oder mehrere Schaltflächen sowie Text. 

Dieses Codebeispiel zeigt, wie Sie eine Antwortnachricht erstellen, die zwei Miniaturbildkarten im Listenformat enthält: 

[!code-csharp[Add ThumbnailCard attachment](../includes/code/dotnet-add-attachments.cs#addThumbnailCardAttachment)]

## <a name="add-a-receipt-card-to-a-message"></a>Hinzufügen einer Quittungskarte zu einer Nachricht

Mit der Quittungskarte kann ein Bot Quittungen für Benutzer ausstellen. Sie enthalten in der Regel die Liste der Elemente, die in der Quittung enthalten sein sollen, Steuer- und Gesamtbeträge sowie anderen Text. 

Dieses Codebeispiel zeigt, wie Sie eine Antwortnachricht erstellen, die eine Quittungskarte enthält: 

[!code-csharp[Add ReceiptCard attachment](../includes/code/dotnet-add-attachments.cs#addReceiptCardAttachment)]

## <a name="add-a-sign-in-card-to-a-message"></a>Hinzufügen einer Anmeldekarte zu einer Nachricht

Mit der Anmeldekarte kann ein Bot die Anmeldung eines Benutzers anfordern. Sie enthalten in der Regel Text und eine oder mehrere Schaltflächen, auf die der Benutzer klicken kann, um den Anmeldevorgang zu initiieren. 

Dieses Codebeispiel zeigt, wie Sie eine Antwortnachricht erstellen, die eine Anmeldekarte enthält:

[!code-csharp[Add SignInCard attachment](../includes/code/dotnet-add-attachments.cs#addSignInCardAttachment)]

## <a id="adaptive-card"></a> Hinzufügen einer adaptiven Karte zu einer Nachricht

Adaptive Karten können eine beliebige Kombination von Text, Sprache, Bildern, Schaltflächen und Eingabefeldern enthalten. Sie werden mit dem unter <a href="http://adaptivecards.io" target="_blank">Adaptive Karten</a> angegebenen JSON-Format erstellt, das Ihnen die vollständige Steuerung über Inhalt und Format der Karte gibt. 

Installieren Sie das NuGet-Paket `Microsoft.AdaptiveCards`, um eine adaptive Karte mit .NET zu erstellen. Auf der Website<a href="http://adaptivecards.io" target="_blank">Adaptive Karten</a> finden Sie Informationen zum Schema adaptiver Karten und zum Untersuchen adaptiver Kartenelemente sowie JSON-Beispiele, mit deren Hilfe Sie Karten mit unterschiedlichem Aufbau und unterschiedlicher Komplexität erstellen können. Darüber hinaus können Sie die interaktive Schnellansicht verwenden, um Nutzlasten adaptiver Karten zu entwerfen und die Kartenausgabe in der Vorschau anzuzeigen.

Dieses Codebeispiel zeigt, wie Sie eine Nachricht erstellen, die eine adaptive Karte für eine Kalendererinnerung enthält: 

[!code-csharp[Add Adaptive Card attachment](../includes/code/dotnet-add-attachments.cs#addAdaptiveCardAttachment)]

Die daraus resultierende Karte enthält drei Textblöcke, ein Eingabefeld (Auswahlliste) und drei Schaltflächen:

![Adaptive Karte für eine Kalendererinnerung](../media/adaptive-card-reminder.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Funktionsvorschau mit Channel Inspector][inspector]
- <a href="http://adaptivecards.io" target="_blank">Adaptive Karten</a>
- [Übersicht über Aktivitäten](bot-builder-dotnet-activities.md)
- [Erstellen von Nachrichten](bot-builder-dotnet-create-messages.md)
- [Hinzufügen von Medienanlagen zu Nachrichten](bot-builder-dotnet-add-media-attachments.md)
- <a href="https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Activity-Klasse</a>
- <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.connector.attachments?view=botconnector-3.12.2.4" target="_blank">Attachment-Klasse</a>

[animationCard]: /dotnet/api/microsoft.bot.connector.animationcard

[audioCard]: /dotnet/api/microsoft.bot.connector.audiocard 

[heroCard]: /dotnet/api/microsoft.bot.connector.herocard 

[thumbnailCard]: /dotnet/api/microsoft.bot.connector.thumbnailcard 

[receiptCard]: /dotnet/api/microsoft.bot.connector.receiptcard 

[signinCard]: /dotnet/api/microsoft.bot.connector.signincard 

[videoCard]: /dotnet/api/microsoft.bot.connector.videocard

[inspector]: ../bot-service-channel-inspector.md

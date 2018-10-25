---
title: Anpassen eines Formulars mithilfe von FormBuilder | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Konversationsfluss und entsprechende Inhalte mithilfe von FormBuilder für das Bot Builder SDK für .NET dynamisch ändern und anpassen.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 1c4e60f76ecebfa01664500b8343d60ccff0064c
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49997697"
---
# <a name="customize-a-form-using-formbuilder"></a>Anpassen eines Formulars mithilfe von FormBuilder

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Im Artikel [Grundlegende Funktionen von FormFlow](bot-builder-dotnet-formflow.md) wird eine grundlegende FormFlow-Implementierung beschrieben, die eine relativ allgemeine Benutzeroberfläche bereitstellt, und im Artikel [Erweiterte Funktionen von FormFlow](bot-builder-dotnet-formflow-advanced.md) wird beschrieben, wie Sie die Benutzeroberfläche mithilfe von Geschäftslogik und Attributen anpassen können. In diesem Artikel wird beschrieben, wie Sie die Benutzeroberfläche mit [FormBuilder][formBuilder] noch mehr anpassen können, indem Sie die Reihenfolge angeben, in der das Formular Schritte ausführt, sowie Feldwerte, Bestätigungen und Meldungen dynamisch definieren. 

## <a name="dynamically-define-field-values-confirmations-and-messages"></a>Dynamisches Definieren von Feldwerten, Bestätigungen und Meldungen

Mit FormBuilder können Sie Feldwerte, Bestätigungen und Meldungen dynamisch definieren.

### <a name="dynamically-define-field-values"></a>Dynamisches Definieren von Feldwerten 

Ein Sandwichbot, der entwickelt wurde, um ein kostenloses Getränk oder Plätzchen zu jeder Bestellung hinzuzufügen, die ein großes belegtes Sandwich enthält, verwendet das Feld `Sandwich.Specials` zum Speichern von Daten zu Gratisprodukten. In diesem Fall muss der Wert des Felds `Sandwich.Specials` für jede Bestellung dahingehend dynamisch festgelegt werden, ob die Bestellung ein großes belegtes Sandwich enthält oder nicht. 

`Specials` wird als optionales Feld und „None“ (Keine) als Text für die Auswahl angegeben, die keine Einstellung angibt.

[!code-csharp[Field definition](../includes/code/dotnet-formflow-formbuilder.cs#fieldDefinition)]

In diesem Codebeispiel wird gezeigt, wie Sie den Wert des Felds `Specials` dynamisch festlegen. 

[!code-csharp[Define value](../includes/code/dotnet-formflow-formbuilder.cs#defineValue)]

In diesem Beispiel gibt die Methode [Advanced.Field.SetType][setType] den Feldtyp an (`null` stellt ein Enumerationsfeld dar). Die Methode [Advanced.Field.SetActive][setActive] gibt an, dass das Feld nur aktiviert wird, wenn die Größe des Sandwiches `Length.FootLong` ist. Die Methode [Advanced.Field.SetDefine][setDefine] gibt schließlich einen asynchronen Delegaten an, der das Feld definiert. Dem Delegaten wird das aktuelle Statusobjekt und das [Advanced.Field][field] übergeben, das dynamisch definiert wird. Der Delegat verwendet die Fluent-Methoden des Felds, um Werte dynamisch zu definieren. In diesem Beispiel sind die Werte Zeichenfolgen, und die Methoden `AddDescription` und `AddTerms` geben die Beschreibungen und Begriffe für die einzelnen Werte an.

> [!NOTE]
> Um einen Feldwert dynamisch zu definieren, können Sie [Advanced.IField][iField] selbst implementieren oder den Prozess mithilfe der [Advanced.FieldReflector][FieldReflector]-Klasse (wie im obigen Beispiel gezeigt) optimieren. 

### <a name="dynamically-define-messages-and-confirmations"></a>Dynamisches Definieren von Meldungen und Bestätigungen

Mit FormBuilder können Sie auch Meldungen und Bestätigungen dynamisch definieren. Die einzelnen Meldungen und Bestätigungen werden nur ausgeführt, wenn die vorherigen Schritte im Formular inaktiv sind oder abgeschlossen wurden. 

Dieses Codebeispiel zeigt eine dynamisch generierte Bestätigung, die den Preis für das Sandwich berechnet. 

[!code-csharp[Define confirmation](../includes/code/dotnet-formflow-formbuilder.cs#defineConfirmation)]

## <a name="customize-a-form-using-formbuilder"></a>Anpassen eines Formulars mithilfe von FormBuilder

In diesem Codebeispiel wird FormBuilder zum Definieren der Schritte des Formulars, zum [Überprüfen der Auswahl](bot-builder-dotnet-formflow-advanced.md#add-business-logic) und zum [dynamischen Definieren eines Feldwerts und einer Bestätigung](#dynamically-define-field-values-confirmations-and-messages) verwendet. Die Schritte im Formular werden standardmäßig in der Reihenfolge ausgeführt, in der sie aufgelistet sind. Es kann jedoch vorkommen, dass Schritte für Felder übersprungen werden, die bereits Werte enthalten oder wenn eine explizite Navigation angegeben ist. 

[!code-csharp[FormBuilder form](../includes/code/dotnet-formflow-formbuilder.cs#formBuilderForm)]

In diesem Beispiel führt das Formular die folgenden Schritte aus:

- Zeigt eine Willkommensnachricht an. 
- Füllt `SandwichOrder.Sandwich` aus. 
- Füllt `SandwichOrder.Length` aus. 
- Füllt `SandwichOrder.Bread` aus. 
- Füllt `SandwichOrder.Cheese` aus. 
- Füllt `SandwichOrder.Toppings` aus und fügt fehlende Werte hinzu, wenn der Benutzer `ToppingOptions.Everything` ausgewählt hat. -. Zeigt eine Meldung an, in der die ausgewählten Beläge bestätigt werden. 
- Füllt `SandwichOrder.Sauces` aus. 
- [Definiert](#dynamically-define-field-values) den Feldwert für `SandwichOrder.Specials` dynamisch. 
- [Definiert](#dynamically-define-messages-and-confirmations) die Bestätigung für den Preis des Sandwiches dynamisch. 
- Füllt `SandwichOrder.DeliveryAddress` aus und [überprüft](bot-builder-dotnet-formflow-advanced.md#add-business-logic) die resultierende Zeichenfolge. Wenn die Adresse nicht mit einer Zahl beginnt, gibt das Formular eine Meldung zurück. 
- Füllt `SandwichOrder.DeliveryTime` mit einer benutzerdefinierten Eingabeaufforderung aus. 
- Bestätigt die Bestellung. 
- Fügt alle übrigen Felder hinzu, die in der Klasse definiert wurden, aber nicht explizit durch `Field` referenziert werden. (Wenn die Methode `AddRemainingFields` in dem Beispiel nicht aufgerufen wird, würde das Formular keine Felder enthalten, die nicht explizit referenziert wurden.) 
- Zeigt eine Dankesnachricht an. 
- Definiert einen `OnCompletionAsync`-Handler zur Bearbeitung der Bestellung. 

## <a name="sample-code"></a>Beispielcode

[!INCLUDE [Sample code](../includes/snippet-dotnet-formflow-samples.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Grundlegende Funktionen von FormFlow](bot-builder-dotnet-formflow.md)
- [Erweiterte Funktionen von FormFlow](bot-builder-dotnet-formflow-advanced.md)
- [Lokalisieren von Formularinhalten](bot-builder-dotnet-formflow-localize.md)
- [Definieren eines Formulars mit dem JSON-Schema](bot-builder-dotnet-formflow-json-schema.md)
- [Anpassen der Benutzeroberfläche mit Mustersprache](bot-builder-dotnet-formflow-pattern-language.md)
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referenz zum Bot Builder SDK für .NET</a>

[formBuilder]: /dotnet/api/microsoft.bot.builder.formflow.formbuilder-1

[setType]: /dotnet/api/microsoft.bot.builder.formflow.advanced.field-1.settype

[setActive]: /dotnet/api/microsoft.bot.builder.formflow.advanced.field-1.setactive

[setDefine]: /dotnet/api/microsoft.bot.builder.formflow.advanced.field-1.setdefine

[field]: /dotnet/api/microsoft.bot.builder.formflow.advanced.field-1

[iField]: /dotnet/api/microsoft.bot.builder.formflow.advanced.ifield-1

[FieldReflector]: /dotnet/api/microsoft.bot.builder.formflow.advanced.fieldreflector-1

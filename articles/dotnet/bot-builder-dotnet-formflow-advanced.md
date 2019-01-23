---
title: Erweiterte Features von FormFlow | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die Benutzererfahrung mit FormFlow und dem Bot Framework SDK für .NET anpassen.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: d04e13babef847a44438e1a748990d7405478fa2
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54225955"
---
# <a name="advanced-features-of-formflow"></a>Erweiterte Features von FormFlow

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

[Grundlegende Features von FormFlow](bot-builder-dotnet-formflow.md) beschreibt eine grundlegende FormFlow-Implementierung, die eine recht allgemeine Benutzererfahrung bietet. Um eine individuellere Benutzererfahrung mit FormFlow bereitzustellen, können Sie den anfänglichen Formularstatus angeben, Geschäftslogik zum Verwalten von Abhängigkeiten zwischen Feldern zum Verarbeiten von Benutzereingaben hinzufügen und mithilfe von Attributen Eingabeaufforderungen anpassen, Vorlagen überschreiben, optionale Felder festlegen, Benutzereingaben auf Übereinstimmung überprüfen und Benutzereingaben validieren. 

## <a name="specify-initial-form-state-and-entities"></a>Angeben des anfänglichen Formularstatus und von Entitäten

Wenn Sie einen [FormDialog][formDialog] starten, können Sie optional eine Instanz Ihres Zustands übergeben. Wenn Sie eine Instanz des Zustands übergeben, überspringt FormFlow standardmäßig Schritte für alle Felder, die bereits Werte enthalten. Der Benutzer wird nach diesen Feldern nicht gefragt. Um das Formular zu zwingen, den Benutzer nach allen Feldern zu fragen (einschließlich der Felder, die bereits im ursprünglichen Zustand Werte enthalten), übergeben Sie [FormOptions.PromptFieldsWithValues][promptFieldsWithValues], wenn Sie `FormDialog` starten. Wenn ein Feld einen anfänglichen Wert enthält, wird dieser Wert in der Eingabeaufforderung als Standardwert verwendet.

Sie können auch [LUIS](https://luis.ai/)-Entitäten zum Binden an den Zustand übergeben. Wenn `EntityRecommendation.Type` ein Pfad zu einem Feld in Ihrer C#-Klasse ist, wird `EntityRecommendation.Entity` über die Erkennung zum Binden an das Feld weitergeleitet. FormFlow überspringt Schritte für Felder, die an eine Entität gebunden sind. Nach diesen Feldern wird der Benutzer nicht gefragt. 

## <a name="add-business-logic"></a>Hinzufügen von Geschäftslogik 

Um Abhängigkeiten zwischen Formularfeldern zu behandeln oder während des Abrufens oder Festlegens eines Feldwerts bestimmte Logik anzuwenden, können Sie Geschäftslogik in einer Überprüfungsfunktion angeben. Mit einer Überprüfungsfunktion können Sie den Zustand ändern und ein [ValidateResult][validateResult]-Objekt zurückgeben, das Folgendes enthalten kann: 

- eine Feedback-Zeichenfolge, die den Grund beschreibt, aus dem ein Wert ungültig ist
- einen transformierten Wert
- eine Gruppe von Auswahlmöglichkeiten zum Klarstellen eines Werts

Dieses Codebeispiel zeigt eine Überprüfungsfunktion für das Feld `Toppings`. Wenn die Eingabe für das Feld den Enumerationswert `ToppingOptions.Everything` enthält, stellt die Funktion sicher, dass der Wert des Felds `Toppings` die vollständige Liste der Beläge enthält.

[!code-csharp[Validation function](../includes/code/dotnet-formflow-advanced.cs#validationFunction)]

Neben der Überprüfungsfunktion können Sie das [Term](#match-user-input-using-the-terms-attribute)-Attribut hinzufügen, das mit Benutzerausdrücken wie „alles“ oder „nicht“ übereinstimmt.

[!code-csharp[Terms for Toppings](../includes/code/dotnet-formflow-advanced.cs#toppingsTerms)]

Dieser Codeausschnitt zeigt mit der oben dargestellten Überprüfungsfunktion die Interaktion zwischen Bot und Benutzer, wenn der Benutzer „everything but Jalapenos“ (alles außer Jalapenos) anfordert. 

```console
Please select one or more toppings (current choice: No Preference)
 1. Everything
 2. Avocado
 3. Banana Peppers
 4. Cucumbers
 5. Green Bell Peppers
 6. Jalapenos
 7. Lettuce
 8. Olives
 9. Pickles
 10. Red Onion
 11. Spinach
 12. Tomatoes
> everything but jalapenos
For sandwich toppings you have selected Avocado, Banana Peppers, Cucumbers, Green Bell Peppers, Lettuce, Olives, Pickles, Red Onion, Spinach, and Tomatoes.
```

## <a name="formflow-attributes"></a>FormFlow-Attribute

Sie können Ihrer Klasse diese C#-Attribute hinzufügen, um das Verhalten eines FormFlow-Dialogs anzupassen.

| Attribut | Zweck |
|----|----| 
| [Describe][describeAttribute] | Ändert, wie ein Feld oder ein Wert in einer Vorlage oder Karte angezeigt wird |
| [Numeric][numericAttribute] | Schränkt die zulässigen Werte eines numerischen Felds ein |
| [Optional][optionalAttribute] | Markiert ein Feld als optional |
| [Pattern][patternAttribute] | Definiert einen regulären Ausdruck, um ein Zeichenfolgenfeld zu überprüfen |
| [Prompt][promptAttribute] | Definiert die Eingabeaufforderung für ein Feld |
| [Template][templateAttribute] | Definiert die Vorlage zum Generieren von Eingabeaufforderungen oder Werten in Eingabeaufforderungen |
| [Terms][termsAttribute] | Definiert die Eingabebegriffe, die mit einem Feld oder einem Wert übereinstimmen |

## <a name="customize-prompts-using-the-prompt-attribute"></a>Anpassen von Eingabeaufforderungen mit dem Prompt-Attribut

Für jedes Feld im Formular werden automatisch Standardeingabeaufforderungen generiert, aber Sie können eine benutzerdefinierte Eingabeaufforderung für jedes Feld mit dem `Prompt`-Attribut angeben. Wenn die Standardeingabeaufforderung für das Feld `SandwichOrder.Sandwich` beispielsweise „Please select a sandwich“ (Bitte ein Sandwich auswählen) lautet, können Sie das `Prompt`-Attribut hinzufügen, um eine benutzerdefinierte Ansage für dieses Feld anzugeben.

[!code-csharp[Prompt attribute](../includes/code/dotnet-formflow-advanced.cs#promptAttribute)]

In diesem Beispiel wird [Mustersprache](bot-builder-dotnet-formflow-pattern-language.md) verwendet, um die Eingabeaufforderung dynamisch zur Laufzeit mit Formulardaten auszufüllen: `{&}` wird durch die Beschreibung des Felds ersetzt und `{||}` durch die Liste der Optionen in der Enumeration. 

> [!NOTE]
> Standardmäßig wird die Beschreibung eines Felds aus dem Namen des Felds generiert. Um eine benutzerdefinierte Beschreibung für ein Feld anzugeben, fügen Sie das `Describe`-Attribut hinzu.

Dieser Codeausschnitt zeigt die benutzerdefinierte Eingabeaufforderung, die durch das obige Beispiel angegeben wird.

```console
What kind of sandwich would you like?
1. BLT
2. Black Forest Ham
3. Buffalo Chicken
4. Chicken And Bacon Ranch Melt
5. Cold Cut Combo
6. Meatball Marinara
7. Oven Roasted Chicken
8. Roast Beef
9. Rotisserie Style Chicken
10. Spicy Italian
11. Steak And Cheese
12. Sweet Onion Teriyaki
13. Tuna
14. Turkey Breast
15. Veggie
>
```

Ein `Prompt`-Attribut kann auch Parameter angeben, die die Anzeige der Eingabeaufforderung im Formular beeinflussen. Beispielsweise legt der `ChoiceFormat`-Parameter fest, wie das Formular die Liste von Auswahlmöglichkeiten rendert.

[!code-csharp[Prompt attribute ChoiceFormat parameter](../includes/code/dotnet-formflow-advanced.cs#promptChoice)]

In diesem Beispiel gibt der Wert des `ChoiceFormat`-Parameters an, dass die Optionen als Liste mit Aufzählungszeichen (anstelle einer nummerierten Liste) angezeigt werden sollen.

```console
What kind of sandwich would you like?
- BLT
- Black Forest Ham
- Buffalo Chicken
- Chicken And Bacon Ranch Melt
- Cold Cut Combo
- Meatball Marinara
- Oven Roasted Chicken
- Roast Beef
- Rotisserie Style Chicken
- Spicy Italian
- Steak And Cheese
- Sweet Onion Teriyaki
- Tuna
- Turkey Breast
- Veggie
>
```

## <a name="customize-prompts-using-the-template-attribute"></a>Anpassen von Eingabeaufforderungen mit dem Template-Attribut

Während Sie mit dem `Prompt`-Attribut die Eingabeaufforderung für ein einzelnes Feld anpassen können, ermöglicht Ihnen das `Template`-Attribut, die Standardvorlagen zu ersetzen, mit denen FormFlow automatisch Eingabeaufforderungen generiert. Dieses Codebeispiel verwendet das `Template`-Attribut, um die Behandlung von Enumerationsfeldern durch das Formular neu zu definieren. Das Attribut gibt an, dass der Benutzer nur ein Element auswählen kann, legt den Text der Eingabeaufforderung mithilfe von [Mustersprache](bot-builder-dotnet-formflow-pattern-language.md) fest und gibt an, dass das Formular mit nur einem Element pro Zeile angezeigt werden soll. 

[!code-csharp[Template attribute](../includes/code/dotnet-formflow-advanced.cs#templateAttribute)]

Dieser Codeausschnitt zeigt die resultierenden Eingabeaufforderungen für die Felder `Bread` und `Cheese`.

```console
What kind of bread would you like on your sandwich?
 1. Nine Grain Wheat
 2. Nine Grain Honey Oat
 3. Italian
 4. Italian Herbs And Cheese
 5. Flatbread
> 

What kind of cheese would you like on your sandwich? 
 1. American
 2. Monterey Cheddar
 3. Pepperjack
> 
```

Wenn Sie das `Template`-Attribut zum Ersetzen der Standardvorlagen, mit denen FormFlow die Eingabeaufforderungen generiert, verwenden, sollten Sie die Eingabeaufforderungen und Nachrichten, die das Formular generiert, mit Variationen versehen. Dazu können Sie mehrere Textzeichenfolgen mithilfe von [Mustersprache](bot-builder-dotnet-formflow-pattern-language.md) definieren, und das Formular wählt immer, wenn eine Eingabeaufforderung oder eine Meldung angezeigt werden soll, nach dem Zufallsprinzip zwischen den verfügbaren Optionen aus.

Dieses Codebeispiel definiert die Vorlage [TemplateUsage.NotUnderstood][notUnderstood] neu, sodass zwei verschiedene Varianten der Nachricht angegeben werden. Wenn der Bot mitteilen muss, dass die Eingabe eines Benutzers nicht verstanden wurde, wird der Inhalt der Nachricht durch Auswählen einer der beiden Textzeichenfolgen nach dem Zufallsprinzip festgelegt. 

[!code-csharp[Template variations of message](../includes/code/dotnet-formflow-advanced.cs#templateMessages)]

Im folgenden Codeausschnitt wird ein Beispiel für die resultierende Interaktion zwischen Bot und Benutzer gezeigt. 

```console
What size of sandwich do you want? (1. Six Inch, 2. Foot Long)
> two feet
I do not understand "two feet".
> two feet
Try again, I don't get "two feet"
> 
```

## <a name="designate-a-field-as-optional-using-the-optional-attribute"></a>Festlegen eines Felds als optional mit dem Optional-Attribut

Um ein Feld als optional festzulegen, verwenden Sie das `Optional`-Attribut. Dieses Codebeispiel gibt an, dass das Feld `Cheese` optional ist.

[!code-csharp[Optional attribute](../includes/code/dotnet-formflow-advanced.cs#optionalAttribute)]

Wenn ein Feld optional ist und kein Wert angegeben wurde, wird die aktuelle Auswahl als „No Preference“ (Kein bevorzugter Inhalt) angezeigt.

```console
What kind of cheese would you like on your sandwich? (current choice: No Preference)
  1. American
  2. Monterey Cheddar
  3. Pepperjack
 >
```

Wenn ein Feld optional ist und der Benutzer einen Wert angegeben hat, wird „No Preference“ als letzte Auswahlmöglichkeit in der Liste angezeigt.

```console
What kind of cheese would you like on your sandwich? (current choice: American)
 1. American
 2. Monterey Cheddar
 3. Pepperjack
 4. No Preference
>
```

## <a name="match-user-input-using-the-terms-attribute"></a>Überprüfen der Benutzereingabe auf Übereinstimmung mit dem Terms-Attribut

Wenn ein Benutzer eine Nachricht an einen Bot sendet, der mit FormFlow erstellt wird, versucht der Bot, die Bedeutung der Benutzereingabe zu identifizieren, indem er die Eingabe mit einer Liste von Begriffen vergleicht. Standardmäßig wird die Liste der Ausdrücke durch Anwenden der folgenden Schritte auf das Feld oder den Wert generiert: 

1. Abbruch bei Änderungen der Groß-/Kleinschreibung und Unterstrich (_)
2. Generieren jedes <a href="https://en.wikipedia.org/wiki/N-gram" target="_blank">N-Gramms</a> bis zu einer maximalen Länge
3. Anfügen von „s?“ am Ende jedes Worts (zur Unterstützung von Pluralformen) 

Der Wert „AngusBeefAndGarlicPizza“ generiert beispielsweise diese Begriffe: 

- „angus?“
- „beefs?“
- „garlics?“
- „pizzas?“
- „angus? beefs?“
- „garlics? pizzas?“
- „angus beef and garlic pizza“

Um dieses Standardverhalten zu überschreiben und die Liste der Begriffe zu definieren, die auf Übereinstimmung mit der Benutzereingabe in einem Feld oder einem Wert in einem Feld überprüft werden, verwenden Sie das `Terms`-Attribut. Beispielsweise können Sie das `Terms`-Attribut mit einem regulären Ausdruck verwenden, um auszugleichen, dass Benutzer wahrscheinlich das Wort „rotisserie“ falsch schreiben. 

[!code-csharp[Terms attribute](../includes/code/dotnet-formflow-advanced.cs#termsAttribute)]

Durch Verwenden des `Terms`-Attributs erhöhen Sie die Wahrscheinlichkeit, eine Übereinstimmung zwischen der Benutzereingabe und einer der gültigen Auswahlmöglichkeiten zu erkennen. Der `Terms.MaxPhrase`-Parameter in diesem Beispiel bewirkt, dass `Language.GenerateTerms` weitere Variationen der Begriffe generiert. 

Im folgenden Codeausschnitt wird die resultierende Interaktion zwischen Bot und Benutzer gezeigt, wenn der Benutzer „rotisserie“ falsch schreibt.

```console
What kind of sandwich would you like?
 1. BLT
 2. Black Forest Ham
 3. Buffalo Chicken
 4. Chicken And Bacon Ranch Melt
 5. Cold Cut Combo
 6. Meatball Marinara
 7. Oven Roasted Chicken
 8. Roast Beef
 9. Rotisserie Style Chicken
 10. Spicy Italian
 11. Steak And Cheese
 12. Sweet Onion Teriyaki
 13. Tuna
 14. Turkey Breast
 15. Veggie
> rotissary checkin
For sandwich I understood Rotisserie Style Chicken. "checkin" is not an option.
```

## <a name="validate-user-input-using-the-numeric-attribute-or-pattern-attribute"></a>Überprüfen der Benutzereingabe mit dem Numeric-Attribut oder dem Pattern-Attribut

Um den Bereich der zulässigen Werte für ein numerisches Feld einzuschränken, verwenden Sie das `Numeric`-Attribut. Dieses Codebeispiel verwendet das `Numeric`-Attribut, um anzugeben, die die Eingabe für das Feld `Rating` eine Zahl zwischen 1 und 5 sein muss. 

[!code-csharp[Numeric attribute](../includes/code/dotnet-formflow-advanced.cs#numericAttribute)]

Um das erforderliche Format für den Wert eines bestimmten Felds anzugeben, verwenden Sie das `Pattern`-Attribut. Dieses Codebeispiel verwendet das `Pattern`-Attribut, um das erforderliche Format für den Wert des Felds `PhoneNumber` anzugeben.

[!code-csharp[Pattern attribute](../includes/code/dotnet-formflow-advanced.cs#patternAttribute)]

## <a name="summary"></a>Zusammenfassung

In diesem Artikel wurde beschrieben, wie Sie eine individuelle Benutzererfahrung mit FormFlow bereitstellen, indem Sie den anfänglichen Formularstatus angeben, Geschäftslogik zum Verwalten von Abhängigkeiten zwischen Feldern zum Verarbeiten von Benutzereingaben hinzufügen und mithilfe von Attributen Eingabeaufforderungen anpassen, Vorlagen überschreiben, optionale Felder festlegen, Benutzereingaben auf Übereinstimmung überprüfen und Benutzereingaben validieren. Informationen zu weiteren Möglichkeiten zum Anpassen der Benutzererfahrung mit FormFlow finden Sie unter [Anpassen eines Formulars mithilfe von FormBuilder](bot-builder-dotnet-formflow-formbuilder.md).

## <a name="sample-code"></a>Beispielcode

[!INCLUDE [Sample code](../includes/snippet-dotnet-formflow-samples.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Grundlegende Funktionen von FormFlow](bot-builder-dotnet-formflow.md)
- [Anpassen eines Formulars mithilfe von FormBuilder](bot-builder-dotnet-formflow-formbuilder.md)
- [Lokalisieren von Formularinhalten](bot-builder-dotnet-formflow-localize.md)
- [Definieren eines Formulars mit dem JSON-Schema](bot-builder-dotnet-formflow-json-schema.md)
- [Anpassen der Benutzeroberfläche mit Mustersprache](bot-builder-dotnet-formflow-pattern-language.md)
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referenz zum Bot Framework SDK für .NET</a>

[formDialog]: /dotnet/api/microsoft.bot.builder.formflow.formdialog

[promptFieldsWithValues]: /dotnet/api/microsoft.bot.builder.formflow.formoptions.promptfieldswithvalues

[validateResult]: /dotnet/api/microsoft.bot.builder.formflow.validateresult

[describeAttribute]: /dotnet/api/microsoft.bot.builder.formflow.describeattribute

[numericAttribute]: /dotnet/api/microsoft.bot.builder.formflow.numericattribute

[optionalAttribute]: /dotnet/api/microsoft.bot.builder.formflow.optionalattribute

[patternAttribute]: /dotnet/api/microsoft.bot.builder.formflow.patternattribute

[promptAttribute]: /dotnet/api/microsoft.bot.builder.formflow.promptattribute

[templateAttribute]: /dotnet/api/microsoft.bot.builder.formflow.templateattribute

[termsAttribute]: /dotnet/api/microsoft.bot.builder.formflow.termsattribute

[notUnderstood]: /dotnet/api/microsoft.bot.builder.formflow.templateusage.notunderstood

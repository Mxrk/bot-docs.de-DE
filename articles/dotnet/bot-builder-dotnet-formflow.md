---
title: Grundlegende Features von FormFlow | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe von FormFlow im Bot Builder SDK für .NET Konversationsflüsse leiten können.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: cf3d69f7941d8c3177788bd00e4b58416a71cf5e
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42904624"
---
# <a name="basic-features-of-formflow"></a>Grundlegende Features von FormFlow

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

[Dialogfelder](bot-builder-dotnet-dialogs.md) sind zwar ein nützliches und flexibles Mittel, aber die Verarbeitung einer geführten Konversation (z.B. zum Bestellen eines Sandwiches) kann sehr viel Mühe kosten. Es gibt jederzeit im Laufe einer Konversation mehrere Möglichkeiten, um fortzufahren. Möglicherweise müssen Unklarheiten geklärt oder Hilfe geleistet werden, oder Sie müssen einen Schritt vor oder zurück gehen. Wenn Sie **FormFlow** im Bot Builder SDK für .NET verwenden, können Sie den Prozess zum Verwalten einer geführten Konversation um einiges vereinfachen. 

FormFlow generiert basierend auf von Ihnen angegebenen Richtlinien automatisch Dialogfelder, die zum Verwalten einer geführten Konversation benötigt werden. Wenn Sie FormFlow verwenden, sind Sie zwar weniger flexibel als wenn Sie Dialogfelder selbst erstellen und verwalten, aber Sie sparen deutlich Zeit beim Entwickeln Ihres Bots, wenn Sie mit FormFlow eine geführte Konversation entwerfen. Außerdem können Sie Ihren Bot mithilfe von Dialogfeldern, die FormFlow generiert, und anderer Dialogfelder erstellen. Beispielsweise unterstützt ein FormFlow-Dialogfeld den Benutzer beim Ausfüllen eines Formulars, während ein [LuisDialog][LuisDialog]-Dialogfeld die Benutzereingabe bewertet, um die jeweilige Absicht zu bestimmen.

In diesem Artikel wird beschrieben, wie Sie einen Bot erstellen können, der grundlegende FormFlow-Features verwendet, um Informationen zu einem Benutzer zu sammeln.

## <a id="forms-and-fields"></a>Formulare und Felder

Sie müssen Informationen angeben, die der Bot zum Benutzer sammeln muss, damit er FormFlow verwenden kann. Wenn der Bot z.B. erstellt wurde, um die Sandwichbestellung eines Benutzers aufzunehmen, müssen Sie ein Formular definieren, das Felder für die Daten enthält, die der Bot benötigt, um die Bestellung abwickeln zu können. Sie können das Formular definieren, indem Sie eine C#-Klasse erstellen, die mindestens eine öffentliche Eigenschaft enthält, um die Daten darzustellen, die der Bot zum Benutzer sammelt. Jede Eigenschaft muss einem der folgenden Typen angehören:

- Integral (sbyte, byte, short, ushort, int, uint, long, ulong)
- Gleitkomma (float, double)
- Zeichenfolge
- Datetime
- Enumeration
- Liste mit Enumerationen

Jeder dieser Datentypen kann Nullwerte zulassen, die Sie verwenden können, um festzulegen, dass dem Feld kein Wert zugeordnet wird. Wenn ein Formularfeld auf einer Enumerationseigenschaft basiert, die keine Nullwerte zulässt, steht der Wert **0** in der Enumeration für **NULL** (d.h., dass dem Feld kein Wert zugeordnet ist). Dann sollten Ihre Enumerationswerte bei **1** beginnen. FormFlow ignoriert alle anderen Eigenschaftstypen und -methoden.

Bei komplexen Objekten müsse Sie einerseits ein Formular für die C#-Klasse auf oberster Ebene und andererseits ein Formular für das komplexe Objekt erstellen. Sie können die Formulare mithilfe von [Dialogsemantik](bot-builder-dotnet-dialogs.md) erstellen. Sie können auch auf direktem Wege ein Formular erstellen, indem Sie [Advanced.IField][iField] implementieren oder [Advanced.Field][field] verwenden und die Wörterbücher damit auffüllen. 

> [!NOTE]
> Sie können Formulare entweder mit einer C#-Klasse oder mit einem JSON-Schema erstellen. In diesem Artikel wird beschrieben, wie Sie ein Formular mit einer C#-Klasse erstellen. Weitere Informationen zum JSON-Schema finden Sie unter [Define a form using JSON schema (Definieren eines Formulars mit dem JSON-Schema)](bot-builder-dotnet-formflow-json-schema.md).

## <a name="simple-sandwich-bot"></a>Einfacher Sandwichbot

Sehen Sie sich dieses Beispiel für einen einfachen Sandwichbot an, der dafür erstellt wurde, die Sandwichbestellung eines Benutzers aufzunehmen. 

### <a id="create-class"></a> Erstellen eines Formulars

Die `SandwichOrder`-Klasse definiert das Formular, und die Enumerationen definieren die Optionen zum Erstellen eines Sandwichs. Diese Klasse umfasst außerdem die statische `BuildForm`-Methode, die [FormBuilder][formBuilder] verwendet, um das Formular zu erstellen und eine einfache Begrüßungsnachricht zu definieren. 

Damit Sie FormFlow verwenden können, müssen Sie zuerst den `Microsoft.Bot.Builder.FormFlow`-Namespace importieren.

[!code-csharp[Define form](../includes/code/dotnet-formflow.cs#defineForm)]

### <a name="connect-the-form-to-the-framework"></a>Herstellen einer Verbindung zwischen dem Formular und dem Framework 

Sie müssen das Formular zum Controller hinzufügen, um eine Verbindung mit dem Framework herstellen zu können. In diesem Beispiel ruft die Methode `Conversation.SendAsync` die statische `MakeRootDialog`-Methode auf, die wiederum die Methode `FormDialog.FromForm` aufruft, um das Formular `SandwichOrder` zu erstellen. 

[!code-csharp[Connect form to framework](../includes/code/dotnet-formflow.cs#connectToFramework)]

### <a name="see-it-in-action"></a>Demo anzeigen

Wenn sie das Formular nur mit einer C#-Klasse definieren und eine Verbindung zum Framework herstellen, kann FormFlow automatisch die Konversation zwischen dem Bot und dem Benutzer verwalten. Die unten aufgeführten Beispielinteraktionen zeigen die Funktionen eines Bots, der mithilfe der grundlegenden Features von FormFlow erstellt wird. Bei jeder Interaktion zeigt ein **>**-Symbol den Punkt an, an dem ein Benutzer eine Antwort eingibt. 

#### <a name="display-the-first-prompt"></a>Anzeigen der ersten Eingabeaufforderung 

Dieses Formular füllt die `SandwichOrder.Sandwich`-Eigenschaft auf. Das Formular generiert die Eingabeaufforderung „Suchen Sie sich ein Sandwich aus“ automatisch. Dabei wird das Wort „Sandwich“ vom Eigenschaftsnamen `Sandwich` abgeleitet. Die `SandwichOptions`-Enumeration definiert die Auswahlmöglichkeiten, die dem Benutzer angezeigt werden. Dabei wird jeder Enumerationswert anhand von Änderungen bei der Groß-/Kleinschreibung oder anhand von Unterstrichen automatisch nach Wörtern aufgeteilt.

```console
Please select a sandwich
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

#### <a name="provide-guidance"></a>Hinzufügen von Anleitungen

Der Benutzer kann jederzeit im Laufe der Konversation das Wort „Hilfe“ eingeben, wenn er eine Anleitung zum Ausfüllen des Formulars benötigt. Wenn der Benutzer z.B in die Sandwicheingabeaufforderung „Hilfe“ eingibt, wird er vom Bot unterstützt. 

```console
> help
* You are filling in the sandwich field. Possible responses:
* You can enter a number 1-15 or words from the descriptions. (BLT, Black Forest Ham, Buffalo Chicken, Chicken And Bacon Ranch Melt, Cold Cut Combo, Meatball Marinara, Oven Roasted Chicken, Roast Beef, Rotisserie Style Chicken, Spicy Italian, Steak And Cheese, Sweet Onion Teriyaki, Tuna, Turkey Breast, and Veggie)
* Back: Go back to the previous question.
* Help: Show the kinds of responses you can enter.
* Quit: Quit the form without completing it.
* Reset: Start over filling in the form. (With defaults from your previous entries.)
* Status: Show your progress in filling in the form so far.
* You can switch to another field by entering its name. (Sandwich, Length, Bread, Cheese, Toppings, and Sauce).
```

#### <a name="advance-to-the-next-prompt"></a>Weiter zur nächsten Eingabeaufforderung

Wenn der Benutzer „2“ in die Antwort auf die erste Sandwicheingabeaufforderung eingibt, zeigt der Bot eine Eingabeaufforderung für die nächste Eigenschaft an, die von dem Formular definiert wird: `SandwichOrder.Length`.

```console
Please select a sandwich
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
> 2
Please select a length (1. Six Inch, 2. Foot Long)
> 
```

#### <a name="return-to-the-previous-prompt"></a>Zurück zur vorherigen Eingabeaufforderung 

Wenn der Benutzer zu diesem Zeitpunkt in der Konversation „Zurück“ eingibt, gibt der Bot die vorherige Eingabeaufforderung zurück. Wenn in der Eingabeaufforderung die aktuelle Auswahl des Benutzers angezeigt wird („Schwarzwälder Schinken“), kann der Benutzer entweder diese Auswahl ändern, indem er eine andere Zahl eingibt, oder die Auswahl bestätigen, indem er „c“ eingibt.

```console
> back
Please select a sandwich(current choice: Black Forest Ham)
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
> c
Please select a length (1. Six Inch, 2. Foot Long)
> 
```

#### <a name="clarify-user-input"></a>Überprüfen der Benutzereingabe

Wenn der Benutzer mit Text anstelle einer Zahl antwortet, um eine Auswahl zu treffen, bittet der Bot automatisch um genauere Angaben, wenn die Benutzereingabe mehr als einer Auswahlmöglichkeit entspricht. 

```console
Please select a bread
 1. Nine Grain Wheat
 2. Nine Grain Honey Oat
 3. Italian
 4. Italian Herbs And Cheese
 5. Flatbread
> nine grain
By "nine grain" bread did you mean (1. Nine Grain Honey Oat, 2. Nine Grain Wheat)
> 1
```

Wenn die Benutzereingabe nicht direkt mit einer der gültigen Auswahlmöglichkeiten übereinstimmt, fordert der Bot den Benutzer automatisch auf, genauere Angaben zu machen.

```console
Please select a cheese (1. American, 2. Monterey Cheddar, 3. Pepperjack)
> amercan
"amercan" is not a cheese option.
> american smoked
For cheese I understood American. "smoked" is not an option.
```

Wenn der Benutzer mehrere Auswahlmöglichkeiten für eine Eigenschaft angibt und der Bot keine dieser Möglichkeiten verarbeiten kann, fordert er den Benutzer automatisch auf, genauere Angaben zu machen.

```console
Please select one or more toppings
 1. Banana Peppers
 2. Cucumbers
 3. Green Bell Peppers
 4. Jalapenos
 5. Lettuce
 6. Olives
 7. Pickles
 8. Red Onion
 9. Spinach
 10. Tomatoes
> peppers, lettuce and tomato
By "peppers" toppings did you mean (1. Green Bell Peppers, 2. Banana Peppers)
> 1
```

#### <a name="show-current-status"></a>Anzeigen des aktuellen Status

Wenn der Benutzer zu einem beliebigen Zeitpunkt während der Bestellung das Wort „Zustand“ eingibt, wird in der Antwort des Bots deutlich, welche Werte angegeben wurden und welche noch noch fehlen. 

```console
Please select one or more sauce
 1. Honey Mustard
 2. Light Mayonnaise
 3. Regular Mayonnaise
 4. Mustard
 5. Oil
 6. Pepper
 7. Ranch
 8. Sweet Onion
 9. Vinegar
> status
* Sandwich: Black Forest Ham
* Length: Six Inch
* Bread: Nine Grain Honey Oat
* Cheese: American
* Toppings: Lettuce, Tomatoes, and Green Bell Peppers
* Sauce: Unspecified  
```

#### <a name="confirm-selections"></a>Bestätigen der Auswahl

Wenn der Benutzer das Formular vollständig ausgefüllt hat, bittet der Bot den Benutzer, die Auswahl zu bestätigen.

```console
Please select one or more sauce
 1. Honey Mustard
 2. Light Mayonnaise
 3. Regular Mayonnaise
 4. Mustard
 5. Oil
 6. Pepper
 7. Ranch
 8. Sweet Onion
 9. Vinegar
> 1
Is this your selection?
* Sandwich: Black Forest Ham
* Length: Six Inch
* Bread: Nine Grain Honey Oat
* Cheese: American
* Toppings: Lettuce, Tomatoes, and Green Bell Peppers
* Sauce: Honey Mustard
>
```

Wenn der Benutzer als Antwort „Nein“ eingibt, bietet der Bot dem Benutzer an, die zuvor getroffene Auswahl zu bestätigen. Wenn der Benutzer als Antwort „Ja“ eingibt, ist das Formular vollständig und das aufrufende Dialogfeld übernimmt die Kontrolle. 

```console
Is this your selection?
* Sandwich: Black Forest Ham
* Length: Six Inch
* Bread: Nine Grain Honey Oat
* Cheese: American
* Toppings: Lettuce, Tomatoes, and Green Bell Peppers
* Sauce: Honey Mustard
> no
What do you want to change?
 1. Sandwich(Black Forest Ham)
 2. Length(Six Inch)
 3. Bread(Nine Grain Honey Oat)
 4. Cheese(American)
 5. Toppings(Lettuce, Tomatoes, and Green Bell Peppers)
 6. Sauce(Honey Mustard)
> 2
Please select a length (current choice: Six Inch) (1. Six Inch, 2. Foot Long)
> 2
Is this your selection?
* Sandwich: Black Forest Ham
* Length: Foot Long
* Bread: Nine Grain Honey Oat
* Cheese: American
* Toppings: Lettuce, Tomatoes, and Green Bell Peppers
* Sauce: Honey Mustard
> y
```

## <a name="handling-quit-and-exceptions"></a>Die Option „Beenden“ und Ausnahmen

Wenn der Benutzer das Wort „Abbrechen“ in das Formular eingibt oder eine Ausnahme zu einem beliebigen Zeitpunkt während der Konversation ausgelöst wird, muss der Bot wissen, in welchem Schritt das Ereignis ausgelöst wurde, in welchem Zustand sich das Formular zu diesem Zeitpunkt befand und welche Schritte vor dem Ereignis erfolgreich abgeschlossen wurden. Das Formular gibt diese Informationen über die `FormCanceledException<T>`-Klasse zurück. 

Das folgende Codebeispiel zeigt, wie Sie die Ausnahme finden und eine Nachricht anzeigen können, die in Abhängigkeit zu dem Ereignis steht, das ausgelöst wurde. 

[!code-csharp[Handle exception or quit](../includes/code/dotnet-formflow.cs#handleExceptionOrQuit)]

## <a name="summary"></a>Zusammenfassung

In diesem Artikel wurde beschrieben, wie Sie die grundlegenden Features von FormFlow verwenden können, um einen Bot zu erstellen, der über die folgenden Funktionen verfügt:

- Automatisches Generieren und Verwalten einer Konversation
- Hilfestellung und Bereitstellen klarer Anweisungen
- Verstehen von Zahlen und Texteingaben
- Bereitstellen von Feedback für den Benutzer im Hinblick auf die Elemente, die verstanden wurden und auf die, die noch verdeutlicht werden müssen 
- Stellen von Fragen, damit Eingaben wenn nötig präzisiert werden 
- Zulassen, dass der Benutzer zwischen den einzelnen Schritten navigieren kann

Obwohl die grundlegenden Features von FormFlow in den meisten Fällen ausreichend sind, ist es möglicherweise vorteilhaft, wenn Sie einige der erweiterten Features von FormFlow in Ihren Bot integrieren. Weitere Informationen finden Sie unter [Advanced features of FormFlow (Erweiterte FormFlow-Features)](bot-builder-dotnet-formflow-advanced.md) und [Customize a form using FormBuilder (Anpassen eines Formulars mithilfe von FormBuilder)](bot-builder-dotnet-formflow-formbuilder.md)

## <a name="sample-code"></a>Beispielcode

[!INCLUDE [Sample code](../includes/snippet-dotnet-formflow-samples.md)]

## <a name="next-steps"></a>Nächste Schritte

FormFlow vereinfacht das Entwickeln von Dialogfeldern. Mithilfe der erweiterten Features von FormFlow können Sie das Verhalten eines FormFlow-Objekts anpassen.

> [!div class="nextstepaction"]
> [Advanced features of FormFlow (Erweiterte Features von FormFlow)](bot-builder-dotnet-formflow-advanced.md)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Anpassen eines Formulars mithilfe von FormBuilder](bot-builder-dotnet-formflow-formbuilder.md)
- [Lokalisieren von Formularinhalten](bot-builder-dotnet-formflow-localize.md)
- [Definieren eines Formulars mit dem JSON-Schema](bot-builder-dotnet-formflow-json-schema.md)
- [Anpassen der Benutzeroberfläche mit Mustersprache](bot-builder-dotnet-formflow-pattern-language.md)
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referenz zum Bot Builder SDK für .NET</a>

[LuisDialog]: /dotnet/api/microsoft.bot.builder.dialogs.luisdialog-1

[iField]: /dotnet/api/microsoft.bot.builder.formflow.advanced.ifield-1

[field]: /dotnet/api/microsoft.bot.builder.formflow.advanced.field-1

[formBuilder]: /dotnet/api/microsoft.bot.builder.formflow.formbuilder-1

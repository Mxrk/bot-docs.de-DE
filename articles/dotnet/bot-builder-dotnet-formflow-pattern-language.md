---
title: Anpassen der Benutzeroberfläche mit Mustersprache | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem Bot Builder SDK für .NET mithilfe von Mustersprache FormFlow-Eingabeaufforderungen anpassen und FormFlow-Vorlagen überschreiben.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 8c1fe15436a00487483755af06897c4af1c70be2
ms.sourcegitcommit: b8bd66fa955217cc00b6650f5d591b2b73c3254b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49326497"
---
# <a name="customize-user-experience-with-pattern-language"></a>Anpassen der Benutzeroberfläche mit Mustersprache

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Beim Anpassen einer Eingabeaufforderung oder beim Überschreiben einer Standardvorlage können Sie mit Mustersprache den Inhalt und/oder das Format der Eingabeaufforderung angeben. 

## <a name="prompts-and-templates"></a>Eingabeaufforderungen und Vorlagen

Eine [Eingabeaufforderung][ promptAttribute] definiert die Nachricht, die dem Benutzer zum Anfordern von Informationen oder einer Bestätigung gesendet wird. Eine Eingabeaufforderung kann mit dem [Prompt-Attribut](bot-builder-dotnet-formflow-advanced.md#customize-prompts-using-the-prompt-attribute) oder implizit über [IFormBuilder<T>.Field][field] angepasst werden. 

Formulare verwenden Vorlagen, um Eingabeaufforderungen und andere Elemente wie Hilfe automatisch zu erstellen. Sie können die Standardvorlage einer Klasse oder eines Felds mit dem [Template-Attribut](bot-builder-dotnet-formflow-advanced.md#customize-prompts-using-the-template-attribute) überschreiben. 

> [!TIP]
> Die [FormConfiguration.Templates][formConfiguration]-Klasse definiert einen Satz integrierter Vorlagen, die gute Beispiele für die Verwendung von Mustersprache bieten.

## <a name="elements-of-pattern-language"></a>Elemente der Mustersprache

Die Mustersprache verwendet geschweifte Klammern (`{}`), um Elemente zu identifizieren, die zur Laufzeit durch tatsächliche Werte ersetzt werden. In der folgenden Tabelle sind die Elemente der Mustersprache aufgeführt.

| Element | BESCHREIBUNG |
|----|----|
| `{<format>}` | Zeigt den Wert des aktuellen Felds an (das Feld, für das das Attribut gilt). |
| `{&}` | Zeigt die Beschreibung des aktuellen Felds an (sofern nicht anders angegeben, handelt es sich dabei um den Namen des Felds). |
| `{<field><format>}` | Zeigt den Wert des benannten Felds an. | 
| `{&<field>}` | Zeigt die Beschreibung des benannten Felds an. |
| <code>{&#124;&#124;}</code> | Zeigt die aktuelle Auswahl an. Dabei kann es sich um den aktuellen Wert eines Felds, „keine Einstellung“ oder die Werte einer Enumeration handeln. |
| `{[{<field><format>} ...]}` | Zeigt eine Liste von Werten aus den benannten Feldern an, wobei [Separator][separator] und [LastSeparator][lastSeparator] zum Trennen der einzelnen Werte in der Liste verwendet werden. |
| `{*}` | Zeigt eine Zeile für jedes aktive Feld an. Jede Zeile enthält die Feldbeschreibung und den aktuellen Wert. | 
| `{*filled}` | Zeigt eine Zeile für jedes aktive Feld an, das einen tatsächlichen Wert enthält. Jede Zeile enthält die Feldbeschreibung und den aktuellen Wert. |
| `{<nth><format>}` | Ein regulärer C#-Formatbezeichner, der sich auf das n-te Argument einer Vorlage bezieht. Die Liste der verfügbaren Argumente finden Sie unter [TemplateUsage][templateUsage]. |
| `{?<textOrPatternElement>...}` | Bedingte Ersetzung. Wenn alle genannten Musterelemente Werte aufweisen, werden die Werte ersetzt, und der gesamte Ausdruck wird verwendet. |

Für die oben aufgeführten Elemente gilt Folgendes:

- Der Platzhalter `<field>` steht für den Namen eines Felds in Ihrer Formularklasse. Wenn Ihre Klasse z. B. ein Feld mit dem Namen `Size` enthält, könnten Sie `{Size}` als Musterelement angeben. 

- Eine Ellipse (`"..."`) in einem Musterelement gibt an, dass das Element mehrere Werte enthalten kann.

- Der Platzhalter `<format>` ist ein C#-Formatbezeichner. Wenn die Klasse z. B. ein Feld mit dem Namen `Rating` und dem Typ `double` enthält, könnten Sie `{Rating:F2}` als Musterelement angeben, um zwei Dezimalstellen anzuzeigen.

- Der Platzhalter `<nth>` verweist auf das n-te Argument einer Vorlage.

### <a name="pattern-language-within-a-prompt-attribute"></a>Mustersprache in einem Prompt-Attribut

In diesem Beispiel werden das Element `{&}` zum Anzeigen der Beschreibung des Felds `Sandwich` und das Element `{||}` zum Anzeigen der Liste der Optionen für dieses Feld verwendet.

[!code-csharp[Patterns example](../includes/code/dotnet-formflow-pattern-language.cs#patterns1)]

Die daraus resultierende Eingabeaufforderung sieht wie folgt aus:

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

## <a name="formatting-parameters"></a>Formatierungsparameter

Eingabeaufforderungen und Vorlagen unterstützen die folgenden Formatierungsparameter.

| Verwendung | BESCHREIBUNG |
|----|----|
| `AllowDefault` | Gilt für Musterelemente des Typs <code>{&#124;&#124;}</code>. Bestimmt, ob auf dem Formular der aktuelle Wert des Felds als mögliche Option angezeigt werden soll. Wenn `true`, wird der aktuelle Wert als möglicher Wert angezeigt. Der Standardwert lautet `true`. |
| `ChoiceCase` | Gilt für Musterelemente des Typs <code>{&#124;&#124;}</code>. Bestimmt, ob der Text jeder Option normalisiert wird, (beispielsweise ob der erste Buchstabe jedes Worts groß geschrieben wird). Der Standardwert lautet `CaseNormalization.None`. Mögliche Werte finden Sie unter [CaseNormalization][caseNormalization]. |
| `ChoiceFormat` | Gilt für Musterelemente des Typs <code>{&#124;&#124;}</code>. Bestimmt, ob eine Liste mit Optionen als nummerierte Liste oder als Aufzählung angezeigt werden soll. Wenn eine nummerierte Liste verwendet werden soll, legen Sie `ChoiceFormat` auf `{0}` (Standardwert) fest. Wenn eine Aufzählung verwendet werden soll, legen Sie `ChoiceFormat` auf `{1}` fest. |
| `ChoiceLastSeparator` | Gilt für Musterelemente des Typs <code>{&#124;&#124;}</code>. Bestimmt, ob eine Inlineliste mit Optionen ein Trennzeichen vor der letzten Option enthält. |
| `ChoiceParens` | Gilt für Musterelemente des Typs <code>{&#124;&#124;}</code>. Bestimmt, ob eine Inlineliste mit Optionen in Klammern angezeigt wird. Wenn `true`, wird die Liste mit Optionen in Klammern angezeigt. Der Standardwert lautet `true`. |
| `ChoiceSeparator` | Gilt für Musterelemente des Typs <code>{&#124;&#124;}</code>. Bestimmt, ob eine Inlineliste mit Optionen ein Trennzeichen vor jeder Option außer der letzten Option enthält. | 
| `ChoiceStyle` | Gilt für Musterelemente des Typs <code>{&#124;&#124;}</code>. Bestimmt, ob die Liste mit Optionen inline oder pro Zeile angezeigt wird. Der Standardwert ist `ChoiceStyleOptions.Auto`, der zur Laufzeit bestimmt, ob die Option inline oder in einer Liste angezeigt wird. Mögliche Werte finden Sie unter [ChoiceStyleOptions][choiceStyleOptions]. |
| `Feedback` | Gilt nur für Eingabeaufforderungen. Bestimmt, ob das Formular die Auswahl des Benutzers wiederholt, um anzugeben, dass das Formular die Auswahl verstanden hat. Der Standardwert ist `FeedbackOptions.Auto`. Dabei wird die Eingabe des Benutzers nur wiederholt, wenn ein Teil davon nicht verstanden wurde. Mögliche Werte finden Sie unter [FeedbackOptions][feedbackOptions]. |
| `FieldCase` | Bestimmt, ob der Text der Feldbeschreibung normalisiert wird, (beispielsweise ob der erste Buchstabe jedes Worts groß geschrieben wird). Der Standardwert ist `CaseNormalization.Lower`, der die Beschreibung in Kleinbuchstaben umwandelt. Mögliche Werte finden Sie unter [CaseNormalization][caseNormalization]. |
| `LastSeparator` | Gilt für Musterelemente des Typs `{[]}`. Bestimmt, ob ein Array von Elementen ein Trennzeichen vor dem letzten Element enthält. |
| `Separator` | Gilt für Musterelemente des Typs `{[]}`. Bestimmt, ob ein Array von Elementen ein Trennzeichen vor jedem Element im Array außer dem letzten Element enthält. |
| `ValueCase` | Bestimmt, ob der Text des Feldwerts normalisiert wird, (beispielsweise ob der erste Buchstabe jedes Worts groß geschrieben wird). Der Standardwert ist `CaseNormalization.InitialUpper`, der festlegt, dass der erste Buchstabe jedes Worts groß geschrieben wird. Mögliche Werte finden Sie unter [CaseNormalization][caseNormalization]. |

### <a name="prompt-attribute-with-formatting-parameter"></a>Prompt-Attribut mit Formatierungsparameter 

In diesem Beispiel wird der Parameter `ChoiceFormat` verwendet, um anzugeben, dass die Liste mit Optionen als Aufzählung angezeigt werden soll.

[!code-csharp[Patterns example](../includes/code/dotnet-formflow-pattern-language.cs#patterns2)]

Die daraus resultierende Eingabeaufforderung sieht wie folgt aus:

```console
What kind of sandwich would you like?
* BLT
* Black Forest Ham
* Buffalo Chicken
* Chicken And Bacon Ranch Melt
* Cold Cut Combo
* Meatball Marinara
* Oven Roasted Chicken
* Roast Beef
* Rotisserie Style Chicken
* Spicy Italian
* Steak And Cheese
* Sweet Onion Teriyaki
* Tuna
* Turkey Breast
* Veggie
>
```

## <a name="sample-code"></a>Beispielcode

[!INCLUDE [Sample code](../includes/snippet-dotnet-formflow-samples.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Grundlegende Funktionen von FormFlow](bot-builder-dotnet-formflow.md)
- [Advanced features of FormFlow (Erweiterte Features von FormFlow)](bot-builder-dotnet-formflow-advanced.md)
- [Customize a form using FormBuilder (Anpassen eines Formulars mithilfe von FormBuilder)](bot-builder-dotnet-formflow-formbuilder.md)
- [Lokalisieren von Formularinhalten](bot-builder-dotnet-formflow-localize.md)
- [Definieren eines Formulars mit dem JSON-Schema](bot-builder-dotnet-formflow-json-schema.md)
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referenz zum Bot Builder SDK für .NET</a>

[promptAttribute]: /dotnet/api/microsoft.bot.builder.formflow.promptattribute

[field]: /dotnet/api/microsoft.bot.builder.formflow.iformbuilder-1.field

[formConfiguration]: /dotnet/api/microsoft.bot.builder.formflow.formconfiguration

[separator]: /dotnet/api/microsoft.bot.builder.formflow.advanced.templatebaseattribute.separator

[lastSeparator]: /dotnet/api/microsoft.bot.builder.formflow.advanced.templatebaseattribute.lastseparator

[templateUsage]: /dotnet/api/microsoft.bot.builder.formflow.templateusage

[caseNormalization]: /dotnet/api/microsoft.bot.builder.formflow.casenormalization

[choiceStyleOptions]: /dotnet/api/microsoft.bot.builder.formflow.choicestyleoptions

[feedbackOptions]: /dotnet/api/microsoft.bot.builder.formflow.feedbackoptions

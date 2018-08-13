---
title: Definieren eines Formulars mit dem JSON-Schema und FormFlow | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe des Bot Builder SDK für .NET mit dem JSON-Schema und FormFlow ein Formular definieren.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: a7f6e3f186e0c4b9f6096cad72a91ef6f3fdffd4
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39303869"
---
# <a name="define-a-form-using-json-schema"></a>Definieren eines Formulars mit dem JSON-Schema

Wenn Sie beim Erstellen eines Bots mit FormFlow eine [C#-Klasse](bot-builder-dotnet-formflow.md#create-class) zum Definieren des Formulars verwenden, wird das Formular von der statischen Definition Ihres Typs in C# abgeleitet. Als Alternative können Sie das Formular stattdessen mit dem <a href="http://json-schema.org/documentation.html" target="_blank">JSON-Schema</a> definieren. Ein mit dem JSON-Schema definiertes Formular ist ausschließlich datengesteuert. Sie können das Formular (und somit das Verhalten des Bots) einfach durch Aktualisieren des Schemas ändern. 

Das JSON-Schema beschreibt die Felder in Ihrem <a href="http://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JObject.htm" target="_blank">JObject</a> und enthält Anmerkungen, die Eingabeaufforderungen, Vorlagen und Begriffe steuern. Um das JSON-Schema mit FormFlow verwenden zu können, müssen Sie Ihrem Projekt das NuGet-Paket `Microsoft.Bot.Builder.FormFlow.Json` hinzufügen und den Namespace `Microsoft.Bot.Builder.FormFlow.Json` importieren.

## <a name="standard-keywords"></a>Standardschlüsselwörter 

FormFlow unterstützt die folgenden <a href="http://json-schema.org/documentation.html" target="_blank">JSON-Schema</a>-Standardschlüsselwörter:

| Schlüsselwort | Beschreibung | 
|----|----|
| type | Definiert den Typ der im Feld enthaltenen Daten. |
| enum | Definiert die gültigen Werte für das Feld. |
| minimum | Definiert den für das Feld zulässigen numerischen Mindestwert (wie unter [NumericAttribute][numericAttribute] beschrieben). |
| maximum | Definiert den für das Feld zulässigen numerischen Höchstwert (wie unter [NumericAttribute][numericAttribute] beschrieben). |
| required | Definiert, welche Felder erforderlich sind. |
| pattern | Überprüft Zeichenfolgenwerte (wie unter [PatternAttribute][patternAttribute] beschrieben). |

## <a name="extensions-to-json-schema"></a>Erweiterungen für das JSON-Schema

FormFlow erweitert das standardmäßige <a href="http://json-schema.org/documentation.html" target="_blank">JSON-Schema</a>, um mehrere zusätzliche Eigenschaften zu unterstützen.

### <a name="additional-properties-at-the-root-of-the-schema"></a>Zusätzliche Eigenschaften im Schemastamm

| Eigenschaft | Wert |
|----|----|
| OnCompletion | C#-Skript mit Argumenten `(IDialogContext context, JObject state)` zum Ausfüllen des Formulars. |
| References | In Skripts einzuschließende Verweise. Beispiel: `[assemblyReference, ...]`. Pfade sollten absolut oder relativ zum aktuellen Verzeichnis sein. Standardmäßig enthält das Skript `Microsoft.Bot.Builder.dll`. |
| Importe | In Skripts einzuschließende Importe. Beispiel: `[import, ...]`. Standardmäßig enthält das Skript die Namespaces `Microsoft.Bot.Builder`, `Microsoft.Bot.Builder.Dialogs`, `Microsoft.Bot.Builder.FormFlow`, `Microsoft.Bot.Builder.FormFlow.Advanced`, `System.Collections.Generic` und `System.Linq`. |

### <a name="additional-properties-at-the-root-of-the-schema-or-as-peers-of-the-type-property"></a>Zusätzliche Eigenschaften im Schemastamm oder als Peers der „type“-Eigenschaft

| Eigenschaft | Wert |
|----|----|
| Vorlagen | `{ TemplateUsage: { Patterns: [string, ...], <args> }, ...}` |
| Prompt | `{ Patterns:[string, ...] <args>}` |

Verwenden Sie zum Angeben von Vorlagen und Eingabeaufforderungen im JSON-Schema das gleiche Vokabular wie unter [TemplateAttribute][templateAttribute] und [PromptAttribute][promptAttribute] definiert. Die Namen und Werte der Eigenschaften im Schema sollten mit den Namen und Werten der Eigenschaften in der zugrunde liegenden C#-Enumeration übereinstimmen. Im folgenden Codeausschnitt des Schemas wird z. B. eine Vorlage definiert, die die Vorlage `TemplateUsage.NotUnderstood` überschreibt und einen `TemplateBaseAttribute.ChoiceStyle` angibt: 

```json
"Templates":{ "NotUnderstood": { "Patterns": ["I don't get it"], "ChoiceStyle":"Auto"}}
```

### <a name="additional-properties-as-peers-of-the-type-property"></a>Zusätzliche Eigenschaften als Peers der „type“-Eigenschaft

|   Eigenschaft   |          Inhalt:           |                                                   Beschreibung                                                    |
|--------------|-----------------------------|------------------------------------------------------------------------------------------------------------------|
|   Datetime   |            bool             |                                  Gibt an, ob es sich bei dem Feld um ein Feld des Typs `DateTime` handelt.                                  |
|   Describe   |      Zeichenfolge oder Objekt       |                  Beschreibung eines Felds (wie unter [DescribeAttribute][describeAttribute] beschrieben).                  |
|    Begriffe     |       `[string,...]`        |                  Reguläre Ausdrücke für den Abgleich eines Feldwerts (wie unter „TermsAttribute“ beschrieben).                  |
|  MaxPhrase   |             int             |                  Führt Ihre Begriffe durch `Language.GenerateTerms(string, int)` aus, um sie zu erweitern.                   |
|    Werte    | „{ string: {Describe:string |                                  object, Terms:[string, ...], MaxPhrase}, ...}“                                  |
|    Aktiv    |           script            | C#-Skript mit Argumenten `(JObject state)->bool` zum Testen, ob das Feld, die Meldung oder die Bestätigung aktiv ist.  |
|   Überprüfen   |           script            |      C#-Skript mit Argumenten `(JObject state, object value)->ValidateResult` zum Überprüfen eines Feldwerts.      |
|    Define    |           script            |        C#-Skript mit Argumenten `(JObject state, Field<JObject> field)` zum dynamischen Definieren eines Felds.        |
|     Next (Weiter)     |           script            | C#-Skript mit Argumenten `(object value, JObject state)` zur Bestimmung des nächsten Schritts nach dem Ausfüllen eines Felds. |
|    Vorher    |          „[confirm          |                                                  message, ...]“                                                  |
|    Nachher     |          „[confirm          |                                                  message, ...]“                                                  |
| Abhängigkeiten |        [string, ...]        |                           Felder, von denen dieses Feld, diese Meldung oder diese Bestätigung abhängig ist.                           |

Verwenden Sie `{Confirm:script|[string, ...], ...templateArgs}` im Wert der **Before**- oder **After**-Eigenschaft, um eine Bestätigung mithilfe eines C#-Skripts mit dem Argument `(JObject state)` oder einer Gruppe von Mustern zu definieren, die mit optionalen Vorlagenargumenten zufällig ausgewählt werden.

Verwenden Sie `{Message:script|[string, ...] ...templateArgs}` im Wert der **Before**- oder **After**-Eigenschaft, um eine Meldung mithilfe eines C#-Skripts mit dem Argument `(JObject state)` oder einer Gruppe von Mustern zu definieren, die mit optionalen Vorlagenargumenten zufällig ausgewählt werden.

## <a name="scripts"></a>Skripts

Einige der oben beschriebenen Eigenschaften enthalten ein Skript als Eigenschaftswert. Ein Skript kann ein beliebiger Ausschnitt von C#-Code sein, den Sie normalerweise im Text einer Methode finden. Sie können Verweise mithilfe der **References**-Eigenschaft und/oder der **Imports**-Eigenschaft hinzufügen. Im Folgenden werden einige spezielle globale Variablen aufgeführt:

| Variable | Beschreibung |
|----|----|
| choice | Interne Verteilung für das auszuführende Skript. |
| state | `JObject`, Formularstatus für alle Skripts. |
| ifield | `IField<JObject>`, um die Argumentation über das aktuelle Feld für alle Skripts, mit Ausnahme von Generatoren von Eingabeaufforderungen für Meldungen/Bestätigungen zuzulassen. |
| value | Für die **Validate**-Eigenschaft zu überprüfender Objektwert. |
| Feld | `Field<JObject>`, um die dynamische Aktualisierung eines Felds in **Define** zuzulassen. |
| context | Kontext von `IDialogContext`, um die Veröffentlichung von Ergebnissen in **OnCompletion** zuzulassen. |

Über das JSON-Schema definierte Felder können die Definitionen programmatisch erweitern oder überschreiben wie jedes andere Feld. Sie können auch auf die gleiche Weise lokalisiert werden.

## <a name="json-schema-example"></a>Beispiel für das JSON-Schema

Die einfachste Möglichkeit zum Definieren eines Formulars besteht darin, alles (einschließlich C#-Code) direkt im JSON-Schema zu definieren. Im folgenden Beispiel ist das JSON-Schema für den mit Anmerkungen versehenen Sandwichbot dargestellt, der unter [Anpassen eines Formulars mithilfe von FormBuilder](bot-builder-dotnet-formflow-formbuilder.md) beschrieben wird.

```json
{
  "References": [ "Microsoft.Bot.Sample.AnnotatedSandwichBot.dll" ],
  "Imports": [ "Microsoft.Bot.Sample.AnnotatedSandwichBot.Resource" ],
  "type": "object",
  "required": [
    "Sandwich",
    "Length",
    "Ingredients",
    "DeliveryAddress"
  ],
  "Templates": {
    "NotUnderstood": {
      "Patterns": [ "I do not understand \"{0}\".", "Try again, I don't get \"{0}\"." ]
    },
    "EnumSelectOne": {
      "Patterns": [ "What kind of {&} would you like on your sandwich? {||}" ],
      "ChoiceStyle": "Auto"
    }
  },
  "properties": {
    "Sandwich": {
      "Prompt": { "Patterns": [ "What kind of {&} would you like? {||}" ] },
      "Before": [ { "Message": [ "Welcome to the sandwich order bot!" ] } ],
      "Describe": { "Image": "https://placeholdit.imgix.net/~text?txtsize=16&txt=Sandwich&w=125&h=40&txttrack=0&txtclr=000&txtfont=bold" },
      "type": [
        "string",
        "null"
      ],
      "enum": [
        "BLT",
        "BlackForestHam",
        "BuffaloChicken",
        "ChickenAndBaconRanchMelt",
        "ColdCutCombo",
        "MeatballMarinara",
        "OvenRoastedChicken",
        "RoastBeef",
        "RotisserieStyleChicken",
        "SpicyItalian",
        "SteakAndCheese",
        "SweetOnionTeriyaki",
        "Tuna",
        "TurkeyBreast",
        "Veggie"
      ],
      "Values": {
        "RotisserieStyleChicken": {
          "Terms": [ "rotis\\w* style chicken" ],
          "MaxPhrase": 3
        }
      }
    },
    "Length": {
      "Prompt": {
        "Patterns": [ "What size of sandwich do you want? {||}" ]
      },
      "type": [
        "string",
        "null"
      ],
      "enum": [
        "SixInch",
        "FootLong"
      ]
    },
    "Ingredients": {
      "type": "object",
      "required": [ "Bread" ],
      "properties": {
        "Bread": {
          "type": [
            "string",
            "null"
          ],
          "Describe": {
            "Title": "Sandwich Bot",
            "SubTitle": "Bread Picker"
          },
          "enum": [
            "NineGrainWheat",
            "NineGrainHoneyOat",
            "Italian",
            "ItalianHerbsAndCheese",
            "Flatbread"
          ]
        },
        "Cheese": {
          "type": [
            "string",
            "null"
          ],
          "enum": [
            "American",
            "MontereyCheddar",
            "Pepperjack"
          ]
        },
        "Toppings": {
          "type": "array",
          "items": {
            "type": "integer",
            "enum": [
              "Everything",
              "Avocado",
              "BananaPeppers",
              "Cucumbers",
              "GreenBellPeppers",
              "Jalapenos",
              "Lettuce",
              "Olives",
              "Pickles",
              "RedOnion",
              "Spinach",
              "Tomatoes"
            ],
            "Values": {
              "Everything": { "Terms": [ "except", "but", "not", "no", "all", "everything" ] }
            }
          },
          "Validate": "var values = ((List<object>) value).OfType<string>(); var result = new ValidateResult {IsValid = true, Value = values} ; if (values != null && values.Contains(\"Everything\")) { result.Value = (from topping in new string[] {  \"Avocado\", \"BananaPeppers\", \"Cucumbers\", \"GreenBellPeppers\", \"Jalapenos\", \"Lettuce\", \"Olives\", \"Pickles\", \"RedOnion\", \"Spinach\", \"Tomatoes\"} where !values.Contains(topping) select topping).ToList();} return result;",
          "After": [ { "Message": [ "For sandwich toppings you have selected {Ingredients.Toppings}." ] } ]
        },
        "Sauces": {
          "type": [
            "array",
            "null"
          ],
          "items": {
            "type": "string",
            "enum": [
              "ChipotleSouthwest",
              "HoneyMustard",
              "LightMayonnaise",
              "RegularMayonnaise",
              "Mustard",
              "Oil",
              "Pepper",
              "Ranch",
              "SweetOnion",
              "Vinegar"
            ]
          }
        }
      }
    },
    "Specials": {
      "Templates": {
        "NoPreference": { "Patterns": [ "None" ] }
      },
      "type": [
        "string",
        "null"
      ],
      "Active": "return (string) state[\"Length\"] == \"FootLong\";",
      "Define": "field.SetType(null).AddDescription(\"cookie\", DynamicSandwich.FreeCookie).AddTerms(\"cookie\", Language.GenerateTerms(DynamicSandwich.FreeCookie, 2)).AddDescription(\"drink\", DynamicSandwich.FreeDrink).AddTerms(\"drink\", Language.GenerateTerms(DynamicSandwich.FreeDrink, 2)); return true;",
      "After": [ { "Confirm": "var cost = 0.0; switch ((string) state[\"Length\"]) { case \"SixInch\": cost = 5.0; break; case \"FootLong\": cost=6.50; break;} return new PromptAttribute($\"Total for your sandwich is {cost:C2} is that ok?\");" } ]
    },
    "DeliveryAddress": {
      "type": [
        "string",
        "null"
      ],
      "Validate": "var result = new ValidateResult{ IsValid = true, Value = value}; var address = (value as string).Trim(); if (address.Length > 0 && (address[0] < '0' || address[0] > '9')) {result.Feedback = DynamicSandwich.BadAddress; result.IsValid = false; } return result;"
    },
    "PhoneNumber": {
      "type": [ "string", "null" ],
      "pattern": "(\\(\\d{3}\\))?\\s*\\d{3}(-|\\s*)\\d{4}"
    },
    "DeliveryTime": {
      "Templates": {
        "StatusFormat": {
          "Patterns": [ "{&}: {:t}" ],
          "FieldCase": "None"
        }
      },
      "DateTime": true,
      "type": [
        "string",
        "null"
      ],
      "After": [ { "Confirm": [ "Do you want to order your {Length} {Sandwich} on {Ingredients.Bread} {&Ingredients.Bread} with {[{Ingredients.Cheese} {Ingredients.Toppings} {Ingredients.Sauces} to be sent to {DeliveryAddress} {?at {DeliveryTime}}?" ] } ]
    },
    "Rating": {
      "Describe": "your experience today",
      "type": [
        "number",
        "null"
      ],
      "minimum": 1,
      "maximum": 5,
      "After": [ { "Message": [ "Thanks for ordering your sandwich!" ] } ]
    }
  },
  "OnCompletion": "await context.PostAsync(\"We are currently processing your sandwich. We will message you the status.\");"
}
```

## <a name="implement-formflow-with-json-schema"></a>Implementieren von FormFlow mit dem JSON-Schema

Wenn Sie FormFlow mit einem JSON-Schema implementieren möchten, verwenden Sie das Schema `FormBuilderJson`, das die gleiche Fluent-Oberfläche unterstützt wie `FormBuilder`. Im folgenden Codebeispiel wird gezeigt, wie Sie das JSON-Schema für den mit Anmerkungen versehenen Sandwichbot implementieren, der unter [Anpassen eines Formulars mithilfe von FormBuilder](bot-builder-dotnet-formflow-formbuilder.md) beschrieben wird.

[!code-csharp[Use JSON schema](../includes/code/dotnet-formflow-json-schema.cs#useSchema)]

## <a name="sample-code"></a>Beispielcode

[!INCLUDE [Sample code](../includes/snippet-dotnet-formflow-samples.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Grundlegende Funktionen von FormFlow](bot-builder-dotnet-formflow.md)
- [Erweiterte Funktionen von FormFlow](bot-builder-dotnet-formflow-advanced.md)
- [Anpassen eines Formulars mithilfe von FormBuilder](bot-builder-dotnet-formflow-formbuilder.md)
- [Lokalisieren von Formularinhalten](bot-builder-dotnet-formflow-localize.md)
- [Anpassen der Benutzeroberfläche mit Mustersprache](bot-builder-dotnet-formflow-pattern-language.md)
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referenz zum Bot Builder SDK für .NET</a>

[numericAttribute]: /dotnet/api/microsoft.bot.builder.formflow.numericattribute

[patternAttribute]: /dotnet/api/microsoft.bot.builder.formflow.patternattribute

[templateAttribute]: /dotnet/api/microsoft.bot.builder.formflow.templateattribute

[promptAttribute]: /dotnet/api/microsoft.bot.builder.formflow.promptattribute

[describeAttribute]: /dotnet/api/microsoft.bot.builder.formflow.describeattribute

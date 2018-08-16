---
title: Lokalisieren von Formularinhalten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Formularinhalte mit FormFlow und dem Bot Builder SDK für .NET lokalisieren.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: bb0ac4b8e3fa34ec8863bb323ae968db37972a6f
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301877"
---
# <a name="localize-form-content"></a>Lokalisieren von Formularinhalten

Die Lokalisierungssprache eines Formulars wird durch die Eigenschaften [CurrentUICulture](https://msdn.microsoft.com/en-us/library/system.threading.thread.currentuiculture(v=vs.110).aspx) und [CurrentCulture](https://msdn.microsoft.com/en-us/library/system.threading.thread.currentculture(v=vs.110).aspx) des aktuellen Threads festgelegt. Standardmäßig wird die Kultur vom **Locale**-Feld der aktuellen Nachricht abgeleitet. Sie können dieses Standardverhalten überschreiben. Lokalisierte Informationen können je nach Erstellungsmethode für Ihren Bot aus drei unterschiedlichen Quellen stammen, nämlich aus

- den integrierten Lokalisierungsinhalten für **PromptDialog** und **FormFlow**,
- einer Ressourcendatei, die Sie für statische Zeichenfolgen in Ihrem Formular erstellen,
- und aus einer Ressourcendatei mit Zeichenfolgen für dynamisch berechnete Felder, Nachrichten oder Bestätigungen.

## <a name="generate-a-resource-file-for-the-static-strings-in-your-form"></a>Erstellen einer Ressourcendatei für statische Zeichenfolgen in Ihrem Formular

Zu statischen Zeichenfolgen in einem Formular gehören solche, die das Formular aus den Informationen in Ihrer C#-Klasse generiert, und Zeichenfolgen, die Sie als Eingabeaufforderungen, Vorlagen, Nachrichten oder Bestätigungen festlegen. Zeichenfolgen, die aus integrierten Vorlagen generiert werden, werden nicht als statisch betrachtet, da diese Zeichenfolgen bereits lokalisiert sind. Da viele Zeichenfolgen in einem Formular automatisch generiert werden, ist es nicht möglich, die üblichen C#-Ressourcenzeichenfolgen direkt zu verwenden. Stattdessen können Sie eine Ressourcendatei für statische Zeichenfolgen in Ihrem Formular erstellen, indem Sie `IFormBuilder.SaveResources` aufrufen oder das **RView**-Tool verwenden, das im Bot Builder SDK für .NET enthalten ist.

### <a name="use-iformbuildersaveresources"></a>Verwenden von IFormBuilder.SaveResources

Sie können eine Ressourcendatei generieren, indem Sie [IFormBuilder.SaveResources] [saveResources] für ein Formular aufrufen. Dadurch werden die Zeichenfolgen in einer RESX-Datei gespeichert.

### <a name="use-rview"></a>Verwenden von RView

Alternativ können Sie eine Ressourcendatei erstellen, die auf der DLL- oder EXE-Datei basiert. Hierzu verwenden Sie das <a href="https://github.com/Microsoft/BotBuilder/tree/master/CSharp/Tools/RView" target="_blank">RView</a>-Tool, das im Bot Builder SDK für .NET enthalten ist. Führen Sie zum Erstellen der RESX-Datei **rview** aus, und geben Sie die Assembly an, die die statische Formularerstellungsmethode und den Pfad zur Methode enthält. Im folgenden Codeausschnitt wird zeigt, wie Sie die `Microsoft.Bot.Sample.AnnotatedSandwichBot.SandwichOrder.resx`-Ressourcendatei mithilfe von **RView** erstellen. 

```csharp
rview -g Microsoft.Bot.Sample.AnnotatedSandwichBot.dll Microsoft.Bot.Sample.AnnotatedSandwichBot.SandwichOrder.BuildForm
```

Der folgende Ausschnitt stammt aus der RESX-Datei, die durch den **rview**-Befehl erstellt wird.

```xml
<data name="Specials_description;VALUE" xml:space="preserve">
<value>Specials</value>
</data>
<data name="DeliveryAddress_description;VALUE" xml:space="preserve">
<value>Delivery Address</value>
</data>
<data name="DeliveryTime_description;VALUE" xml:space="preserve">
<value>Delivery Time</value>
</data>
<data name="PhoneNumber_description;VALUE" xml:space="preserve">
<value>Phone Number</value>
</data>
<data name="Rating_description;VALUE" xml:space="preserve">
<value>your experience today</value>
</data>
<data name="message0;LIST" xml:space="preserve">
<value>Welcome to the sandwich order bot!</value>
</data>
<data name="Sandwich_terms;LIST" xml:space="preserve">
<value>sandwichs?</value>
</data>
```

## <a name="configure-your-project"></a>Konfigurieren des Projekts

Fügen Sie die erstellte Ressourcendatei Ihrem Projekt hinzu, und legen Sie anschließend die neutrale Sprache fest. Gehen Sie hierzu folgendermaßen vor: 

1. Klicken Sie zuerst mit der rechten Maustaste auf Ihr Projekt und anschließend auf **Anwendung**.
2. Klicken Sie auf **Assemblyinformationen**.
3. Wählen Sie für **Neutrale Sprache** den Wert aus, der mit der Sprache übereinstimmt, in der Sie den Bot entwickelt haben.

Beim Erstellen des Formulars sucht die [IFormBuilder.Build][build]-Methode automatisch nach Ressourcen, die den Formulartypnamen enthalten. Mit diesen Ressourcen werden die statischen Zeichenfolgen im Formular lokalisiert. 

> [!NOTE]
> Dynamisch berechnete Felder, die mithilfe von [Advanced.Field.SetDefine][setDefine] definiert werden (s. [Verwenden von dynamischen Feldern](bot-builder-dotnet-formflow-formbuilder.md#dynamically-define-field-values-confirmations-and-messages)), können nicht wie statische Felder lokalisiert werden. Dies liegt daran, dass Zeichenfolgen für dynamisch berechnete Felder zu dem Zeitpunkt erstellt werden, an dem das Formular automatisch ausgefüllt wird. Sie können dynamisch berechnete Felder jedoch mithilfe der üblichen C#-Lokalisierungsmechanismen lokalisieren.

### <a name="localize-resource-files"></a>Lokalisieren von Ressourcendateien 

Nach dem Hinzufügen von Ressourcendateien zu Ihrem Projekt können Sie sie mithilfe des <a href="https://developer.microsoft.com/en-us/windows/develop/multilingual-app-toolkit" target="_blank">Multilingual App Toolkits (MAT)</a> lokalisieren. Installieren Sie zuerst **MAT**, und aktivieren Sie es anschließend mithilfe der folgenden Schritte für Ihr Projekt:

1. Wählen Sie Ihr Projekt im Projektmappen-Explorer von Visual Studio aus.
2. Klicken Sie auf **Extras** > **Multilingual App Toolkit** > **Enable** (Aktivieren).
3. Klicken Sie mit der rechten Maustaste auf das Projekt, und rufen Sie **Multilingual App Toolkit** > **Add Translations** (Übersetzungen hinzufügen) auf. Dadurch werden branchenübliche <a href="https://en.wikipedia.org/wiki/XLIFF" target="_blank">XLF</a>-Dateien erstellt, die Sie maschinell oder von menschlichen Übersetzern übersetzen lassen können.

> [!NOTE]
> Obwohl in diesem Artikel die Verwendung des Multilingual App Toolkits zum Lokalisieren von Inhalten beschrieben wird, stehen für die Lokalisierung auch andere Implementierungsmöglichkeiten bereit.

## <a name="see-it-in-action"></a>Praxisbeispiel

Mit dem unten stehenden Code, der auf dem Beispiel aus [Anpassen eines Formulars mithilfe von FormBuilder](bot-builder-dotnet-formflow-formbuilder.md) basiert, wird die oben genannte Lokalisierungsvariante implementiert. In diesem Beispiel enthält die `DynamicSandwich`-Klasse (hier nicht gezeigt) die Lokalisierungsinformationen für dynamisch berechnete Felder, Nachrichten und Bestätigungen.

[!code-csharp[Build localized form](../includes/code/dotnet-formflow-localize.cs#buildLocalizedForm)]

Im folgenden Codeausschnitt wird die resultierende Interaktion zwischen Bot und Benutzer gezeigt, wenn für `CurrentUICulture` die Sprache **Französisch** festgelegt ist.

```console
Bienvenue sur le bot d'ordre "sandwich" !
Quel genre de "sandwich" vous souhaitez sur votre "sandwich"?
 1. BLT
 2. Jambon Forêt Noire
 3. Poulet Buffalo
 4. Faire fondre le poulet et Bacon Ranch
 5. Combo de coupe à froid
 6. Boulette de viande Marinara
 7. Poulet rôti au four
 8. Rôti de boeuf
 9. Rotisserie poulet
 10. Italienne piquante
 11. Bifteck et fromage
 12. Oignon doux Teriyaki
 13. Thon
 14. Poitrine de dinde
 15. Veggie
> 2

Quel genre de longueur vous souhaitez sur votre "sandwich"?
 1. Six pouces
 2. Pied Long
> ?
* Vous renseignez le champ longueur.Réponses possibles:
* Vous pouvez saisir un numéro 1-2 ou des mots de la description. (Six pouces, ou Pied Long)
* Retourner à la question précédente.
* Assistance: Montrez les réponses possibles.
* Abandonner: Abandonner sans finir
* Recommencer remplir le formulaire. (Vos réponses précédentes sont enregistrées.)
* Statut: Montrer le progrès en remplissant le formulaire jusqu'à présent.
* Vous pouvez passer à un autre champ en entrant son nom. ("Sandwich", Longueur, Pain, Fromage, Nappages, Sauces, Adresse de remise, Délai de livraison, ou votre expérience aujourd'hui).
Quel genre de longueur vous souhaitez sur votre "sandwich"?
 1. Six pouces
 2. Pied Long
> 1

Quel genre de pain vous souhaitez sur votre "sandwich"?
 1. Neuf grains de blé
 2. Neuf grains miel avoine
 3. Italien
 4. Fromage et herbes italiennes
 5. Pain plat
> neuf
Par pain "neuf" vouliez-vous dire (1. Neuf grains miel avoine, ou 2. Neuf grains de blé)
```

## <a name="sample-code"></a>Beispielcode

[!INCLUDE [Sample code](../includes/snippet-dotnet-formflow-samples.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Basic features of FormFlow (Grundlegende Features von FormFlow)](bot-builder-dotnet-formflow.md)
- [Advanced features of FormFlow (Erweiterte Features von FormFlow)](bot-builder-dotnet-formflow-advanced.md)
- [Anpassen eines Formulars mithilfe von FormBuilder](bot-builder-dotnet-formflow-formbuilder.md)
- [Definieren eines Formulars mit dem JSON-Schema](bot-builder-dotnet-formflow-json-schema.md)
- [Anpassen der Benutzeroberfläche mit Mustersprache](bot-builder-dotnet-formflow-pattern-language.md)
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referenz zum Bot Builder SDK für .NET</a>

[build]: /dotnet/api/microsoft.bot.builder.formflow.formbuilder-1.build 

[setDefine]: /dotnet/api/microsoft.bot.builder.formflow.advanced.field-1.setdefine

[saveResources]: /dotnet/api/microsoft.bot.builder.formflow.iform-1.saveresources

---
title: Definieren einer LUIS-Erkennung als Aufgabenauslöser | Microsoft-Dokumentation
description: Erfahren Sie, wie eine Sprachverständniserkennung mithilfe von LUIS.ai als Aufgabenauslöser eingesetzt werden kann.
author: vkannan
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: 39fe222fb1d54346b33617c425b1fdf2d56daa0d
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39304173"
---
# <a name="define-a-luis-recognizer-as-task-trigger"></a>Definieren einer LUIS-Erkennung als Aufgabenauslöser
> [!IMPORTANT]
> Conversation Designer ist noch nicht für alle Kunden verfügbar. Weitere Informationen zur Verfügbarkeit von Conversation Designer folgen später in diesem Jahr.

Meistens drücken Benutzer ihre Absicht zum Ausführen einer Aufgabe in natürlicher Sprache aus. Mit Conversation Designer können Sie auf einfache Weise ein Modell zum Verstehen von natürlicher Sprache für Ihren Bot einrichten, das von <a href="https://luis.ai" target="_blank">LUIS.ai</a> unterstützt wird.

Wählen Sie zu diesem Zweck den Auslösertyp **User says or types something** (Benutzer sagt etwas oder gibt etwas ein) aus. Dieser gibt Ihnen die Option, den Namen der **Absicht** anzugeben. 

Sie können nach vorhandenen Absichten suchen oder eine neue Absicht im Feld **Which language intent?** (Welche Sprachabsicht?) erstellen.

## <a name="create-a-new-intent"></a>Erstellen einer neuen Absicht

Um eine neue Absicht zu erstellen, geben Sie den Namen für die Absicht ein, und klicken Sie auf **Create new intent** (Neue Absicht erstellen). Geben Sie Beispieläußerungen für die von Ihnen erwarteten möglichen Aussagen der Benutzer ein, die diese bestimmte Aufgabe auslösen sollen.

Beispielsweise sollte ein Café-Bot die Aufgabe lösen können, in der Nähe des Benutzers liegende Cafés anzuzeigen. Um dieses Szenario zu behandeln, wählen Sie **User says or types something** (Benutzer sagt etwas oder gibt etwas ein) aus, und legen Sie den **Intent name** (Absichtsnamen) auf „LocationNearMe“ fest. Geben Sie dann Beispieläußerungen für diese Absicht an. Beispiel:  
- *Suche Standorte in meiner Nähe*
- *Suche Standorte von Cafés in meiner Nähe*
- *Ist die Filiale in Köln jetzt geöffnet?*
- *Gibt es ein Geschäft in Köln?*
- *Welche Cafés sind jetzt geöffnet?*
- *Wo bekomme ich etwas zu essen?*
- *Ich würde gern etwas essen.*
- *Ich habe Hunger*

Geben Sie so viele Äußerungen ein, wie Sie zusammenbekommen, die als Ausdruck der Benutzerabsicht dienen können, diese bestimmte Aufgabe auszulösen.

## <a name="default-intents-provisioned-for-your-bot"></a>Für Ihren Bot bereitgestellte Standardabsichten

Standardmäßig wird Ihr Bot mit 4 Absichten bereitgestellt. 
- **None** (Keine): Dies ist die Fallbackabsicht (Standardwert) für Ihren Bot. Verwenden Sie diese Absicht, um das Erfassen von Vorgängen zu unterstützen, für die der Bot noch keine Reaktion kennt.
- **Hilfe**: Richten Sie diese Absicht mit Beispieläußerungen ein, aus denen sich ablesen lässt, ob der Benutzer Hilfe benötigt. *„Hilfe“, „Ich brauche Hilfe“, „Wie geht es weiter?“, „Ich stecke fest“* usw.
- **Begrüßung**: Richten Sie diese Absicht mit Beispieläußerungen ein, die einer Begrüßungsabsicht entsprechen, wie etwa *„Hi“, „Hallo“, „Guten Morgen“, „Schön, dass Sie hier sind“* usw.
- **Abbrechen**: Mit Beispieläußerungen für die Abbruchsabsicht einrichten. *„Halt“, „Stopp“, „Abbrechen“, „Stornieren“, „Zurück“* usw.

## <a name="create-and-label-entities"></a>Erstellen und Bezeichnen von Entitäten

Abgesehen vom Bestimmen der Benutzerabsicht kann Sprachverständnis Ihnen auch dabei helfen, bestimmte im Zusammenhang der Aufgabe relevante Entitäten zu bestimmen. Wenn ein Benutzer beispielsweise sagt *Suche Cafés im Umkreis von Köln*, kann es sinnvoll sein, *Köln* als einen möglichen Wert für die Entität ***location*** (Standort) zu extrahieren. 

Um Entitäten für die Aufgabe einzurichten, wählen Sie in der Zeichenfolge **Äußerungen** den Teil der Äußerung aus, der ein Beispiel für einen Entitätswert bilden soll. Weisen Sie diesen einer vorhandenen Entität zu, oder erstellen Sie eine neue. Um eine neue Entität zu erstellen, geben Sie den Namen der Entität im Feld **Search or create** (Suchen oder erstellen) ein, und klicken Sie auf **Create new entity** (Neue Entität erstellen). 

# <a name="supported-entity-types"></a>Unterstützte Entitätstypen

Dank Sprachverständnis können Sie verschiedene Typen von Entitäten erstellen. Beim Erstellen einer Entität müssen Sie einen `type` angeben. 

Diese Typen sind verfügbar:

- **Simple** (Einfach): Dies ist der *Standardtyp*.
- **List** (Liste): Verwenden Sie diesen Typ, wenn Ihre Entität eine endliche Menge möglicher Werte aufweist. Beispiel: *Farbe*, *Ort*.
- **Hierarchical** (Hierarchisch): Verwenden Sie diesen Typ, um Entitäten mit Über-/Unterordnungsbeziehungen zu erstellen. Beispiel: *VonOrt* und *NachOrt*, beide haben die Entität *Ort* als übergeordnetes Element.
- **Composite** (Zusammengesetzt): Verwenden Sie diesen Typ, um Gruppen von Werten zu erstellen, die eine sinnvolle Einheit bilden. Beispiel: *Ort* und *Bundesland* bilden zusammen die Entität *Standort*.

<!-- # pre-built entity types TBD -->

# <a name="entities-in-use"></a>Entitäten im Gebrauch

Während Sie Entitäten erstellen und sie dem Sprachverständnisabschnitt hinzufügen, wird die Tabelle **Entities in use** (Entitäten im Gebrauch) mit der Liste der Entitäten aktualisiert, die von dieser bestimmten Aufgabe verwendet werden. Sie können der Liste manuell weitere Entitäten hinzufügen, die in dieser Aufgabe verwendet werden. 

Die verfügbaren Optionen lauten wie folgt:

- **Code**: Dies ist eine Entität, die in Ihrem benutzerdefinierten Skript erstellt wird. Sie können sie hier angeben, um Autorenfunktionen wie IntelliSense zu unterstützen.

<!-- # Use as help tip TBD  -->

## <a name="next-step"></a>Nächster Schritt
> [!div class="nextstepaction"]
> [Code-Erkennung](conversation-designer-code-recognizer.md)

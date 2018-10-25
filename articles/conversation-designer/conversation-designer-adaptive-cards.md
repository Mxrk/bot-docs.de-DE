---
title: Konfigurieren von adaptiven Karten | Microsoft-Dokumentation
description: Erfahren Sie, wie adaptive Karten konfiguriert werden.
author: vkannan
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: d41c2c24ed38fffe76cd73a6bb8a685d3861ac55
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "50000427"
---
# <a name="configure-adaptive-cards"></a>Adaptive Karten konfigurieren
> [!IMPORTANT]
> Conversation Designer ist noch nicht für alle Kunden verfügbar. Weitere Informationen zur Verfügbarkeit von Conversation Designer folgen später in diesem Jahr.

<a href="http://adaptivecards.io" target="_blank">Adaptive Karten</a> sind ein neues Schema, das Karten mit aussagekräftiger Benutzeroberfläche für die Verwendung auf einer Reihe verschiedener Endpunkte definiert, einschließlich Kanälen des Microsoft Bot-Frameworks. 

Conversation Designer stellt eine hochgradig integrierte Umgebung zum Erstellen, vorab Anzeigen und Verwenden von adaptiven Karten in Ihren Bots zur Verfügung. 

Adaptive Karten können an verschiedenen wichtigen Orten definiert werden.

- Eine einfache Antwort auf die Aktion für eine Aufgabe.
- Im Feedbackzustand in einem Dialog.
- In Aufforderungszuständen in einem Dialog. Beachten Sie, dass Aufforderungen getrennte Karten aufweisen können: eine für die Antwort und eine weitere für die erneute Aufforderung.

Um eine adaptive Karte zu definieren, navigieren Sie zu dem relevanten Editor. Durchsuchen Sie die vorhandenen Vorlagen für adaptive Karten, und wählen Sie eine aus, oder erstellen Sie eine eigene im JSON-Code-Editor. 

Während Sie eine Karte erstellen, wird eine aussagekräftige Vorschau der Karte im Erstellungsportal angezeigt.

> [!NOTE]
> Die Funktionen adaptiver Karten befinden sich in fortlaufender Entwicklung. Zurzeit unterstützen nicht alle Kanäle alle Funktionen von adaptiven Karten. Informationen dazu, welche Funktionen von den einzelnen Kanälen unterstützt werden, finden Sie im Abschnitt zum Kanalstatus.

## <a name="input-form"></a>Eingabeformular

Adaptive Karten können Eingabeformulare enthalten. In Conversation Designer sind Formulare in Aufgabenentitäten integriert. Wenn ein Feld beispielsweise die `id` **meinName** aufweist und die Aktion `Submit` des Formulars ausgeführt wird, wird eine `taskEntity` mit dem Namen **meinName** erstellt, die den Wert des Felds enthält. 

Der Codeausschnitt unten zeigt, wie die Entität **myName** (meinName) im Code definiert ist:

``javascript
{
   "type": "Input.Text",
   "id": "myName",
   "placeholder": "Last, First"
}
``

Ferner wird, wenn ein Feld die ID `@task` aufweist, sein Wert als Name einer Aufgabe verwendet. Wenn dieses Feld ausgelöst wird (z.B. durch einen Klick auf eine Schaltfläche), wird die benannte Aufgabe ausgeführt. 

Nehmen Sie diesen Codeausschnitt als Beispiel:

``javascript
{
  'type': 'Action.Submit',
  'title': 'Search',
  'speak': '<s>Search</s>',
  'data': {
    '@task': 'Hotel Search'
  }
}
``

Beim Klicken auf diese Schaltfläche wird eine Sendeaktion ausgelöst, und der Wert von `context.sticky` wird auf `Hotel Search` festgelegt. Dies führt zum Ausführen der Aufgabe **Hotel Search** (Hotelsuche). Achten Sie beim Verwenden dieser Funktion darauf, dass `@task` mit einem Aufgabennamen übereinstimmt, den Sie in Conversation Designer definiert haben.

## <a name="use-entities-and-language-generation-templates"></a>Verwenden von Entitäten und Vorlagen zur Sprachgenerierung
Adaptive Karten unterstützen vollständig die Auflösung mithilfe von Sprachgenerierung.

* `entityName` verwendet Entitäten innerhalb der Karte.
* `responseTemplateName` verwendet Vorlagen für einfache oder bedingte Antworten innerhalb der Karte.

Hier erfahren Sie mehr über adaptive Karten: TODO: Insert link to adaptive cards schema documentation -->

## <a name="sample-adaptive-card-payload"></a>Beispielnutzlast für eine adaptive Karte

Die folgende JSON-Code zeigt die Nutzlast einer adaptiven Karte.

```json
{
    "$schema": "https://microsoft.github.io/AdaptiveCards/schemas/adaptive-card.json",
    "type": "AdaptiveCard",
    "version": "1.0",
    "body": [
        {
            "speak": "<s>Serious Pie is a Pizza restaurant which is rated 9.3 by customers.</s>",
            "type": "ColumnSet",
            "columns": [
                {
                    "type": "Column",
                    "size": "2",
                    "items": [
                        {
                            "type": "TextBlock",
                            "text": "[Greeting], [TimeOfDayTemplate], You can eat in {location}",
                            "weight": "bolder",
                            "size": "extraLarge"
                        },
                        {
                            "type": "TextBlock",
                            "text": "9.3 · $$ · Pizza",
                            "isSubtle": true
                        },
                        {
                            "type": "TextBlock",
                            "text": "[builtin.feedback.display]",
                            "wrap": true
                        }
                    ]
                },
                {
                    "type": "Column",
                    "size": "1",
                    "items": [
                        {
                            "type": "Image",
                            "url": "http://res.cloudinary.com/sagacity/image/upload/c_crop,h_670,w_635,x_0,y_0/c_scale,w_640/v1397425743/Untitled-4_lviznp.jpg",
                            "size":"auto"
                        }
                    ]
                }
            ]
        }
    ],
    "actions": [
        {
            "type": "Action.Http",
            "method": "POST",
            "title": "More Info",
            "url": "http://foo.com"
        },
        {
            "type": "Action.Http",
            "method": "POST",
            "title": "View on Foursquare",
            "url": "http://foo.com"
        }
    ]
}
```


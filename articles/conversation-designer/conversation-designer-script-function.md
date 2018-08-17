---
title: Definieren einer Skriptfunktion als Do-Aktion | Microsoft-Dokumentation
description: Erfahren Sie, wie eine Funktion einer Skriptaktion als Do-Aktion eingerichtet wird.
author: vkannan
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: 674781c2cb5a8700941feb59b2ce7ab902979d4f
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39304088"
---
# <a name="define-a-script-function-as-a-do-action"></a>Definieren einer Skriptfunktion als Do-Aktion

[Funktionen von Skriptaktionen](conversation-designer-context-object.md#script-callback-functions) führen benutzerdefinierten Skriptcode aus, den Sie definieren, um die Ausführung der Aufgabe zu unterstützen. Beispielsweise Aufruf eines Diensts, um den Thermostat wirklich auf 23 Grad einzustellen, wenn der Benutzer sagt „Stelle meinen Thermostaten auf 23 Grad ein“. 

Um benutzerdefinierten Skriptcode als Aktion zu verwenden, wählen Sie als **Skriptaktion** den Aktionstyp **DO** im Aufgaben-Editor aus. Geben Sie den Namen der Funktion ein, die die Aktion implementiert. Klicken Sie auf **Bearbeiten**, um den **Skript-** Editor aufzurufen und Ihre Implementierung für diese Funktion zu schreiben. 

## <a name="script-action-function-parameter"></a>Parameter der Skriptaktionsfunktion

Die angegebene Aktionsrückruffunktion wird immer mit dem [`context`](conversation-designer-context-object.md)-Objekt aufgerufen.

Das `context`-Objekt enthält sowohl `taskEntities` als auch `contextEntities`. Aufgabenentitäten sind für diese Aufgabe definierte generierte Entitäten, und Kontextentitäten bilden einen Eigenschaftsbehälter, in dem Entitäten für Konversationen mit dem Benutzer erhalten bleiben können.

## <a name="return-value"></a>Rückgabewert
Es wird erwartet, dass **Skriptaktionsfunktionen** einen booleschen Wert zurückgeben.

## <a name="sample-script-action-function"></a>Beispiel für eine Skriptaktionsfunktion
Die folgende Beispielfunktion nimmt einen HTTP-Aufruf vor, um eine Antwort abzurufen, bevor sie einen angepassten Trigger zurückgibt.

```javascript
module.exports.NewTask_do_onRun = function(context) {
    var options =  {
        host: 'HOST',
        path: 'PATH',
        method: 'post',
        headers: {
            "HEADER1" : "VALUE"
        }, 
        body: {
            "BODY": VALUE
        }
    };

    return http.request(options).then(function(response) {
      // parse response payload
      // Send a message to user
      context.responses.push({text: "Done", type: "message"});
    });
} 
```

## <a name="next-step"></a>Nächster Schritt
> [!div class="nextstepaction"]
> [Dialogaktion](conversation-designer-dialogues.md)

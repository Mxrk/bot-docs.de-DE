---
title: Definieren einer Code-Erkennung als Aufgabenauslöser | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine benutzerdefinierte Code-Erkennung als Aufgabenauslöser verwenden.
author: vkannan
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: 6d44cd299e46971b2218bb78d16e454d5df3c1d9
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39298797"
---
# <a name="define-code-recognizer-as-task-trigger"></a>Definieren einer Code-Erkennung als Aufgabenauslöser
> [!IMPORTANT]
> Conversation Designer ist noch nicht für alle Kunden verfügbar. Weitere Informationen zur Verfügbarkeit von Conversation Designer folgen später in diesem Jahr.

Code-Erkennungen ermöglichen Ihnen das Schreiben von benutzerdefinierten Skripts zum Unterstützen der Ausführung einer Aufgabe. Anhand der Auswertung von Ausdrücken auf Basis des regulären Ausdrucks oder des Aufrufens anderer Dienste kann die Absicht des Benutzers ermittelt werden. Das benutzerdefinierte Skript weist die Konversationsruntime an, eine Aufgabe auszulösen. 

## <a name="create-a-script-function"></a>Erstellen einer Skriptfunktion
Zur Verwendung eines Skripts als Erkennung wählen Sie im Aufgabeneditor die Option „Skriptfunktion“ als Erkennung, geben den Namen der Funktion an und klicken auf **Create/view function** (Funktion erstellen/anzeigen), um mit der Bearbeitung eines Skripts zu beginnen. Sie können auch ohne Angabe eines Skriptnamens auf **Create/view function** klicken, dann wird eine leere Funktion mit dem Standardnamen für Sie erstellt. 

## <a name="script-trigger-function-parameter"></a>Parameter der Skriptauslöserfunktion

Die angegebene Skript-Rückruffunktion wird immer mit dem [`context`](conversation-designer-context-object.md)-Objekt aufgerufen.

Das `context`-Objekt enthält sowohl `taskEntities` als auch `contextEntities`. Aufgabenentitäten sind für diese Aufgabe definierte generierte Entitäten, und Kontextentitäten sind Eigenschaftenbehälter, in denen Entitäten für Konversationen mit dem Benutzer beibehalten werden können.

## <a name="return-value"></a>Rückgabewert

Es wird erwartet, dass **Skriptauslöserfunktionen** einen booleschen Wert zurückgeben.

## <a name="sample-regex-based-recognizer"></a>Beispielerkennung auf Basis des regulären Ausdrucks
Im folgenden Codebeispiel wird der reguläre Ausdruck zum Verarbeiten der Anforderung verwendet und ein boolescher Wert zurückgegeben, sodass die Konversationsruntime den auszuführenden Skriptauslöser ermitteln kann.

```javascript
module.exports.fnFindPhoneTrigger = function(context) {
    if (context.request.text.includes("call") || context.request.text.includes("ring")) {
        return true;
    } else {
        return false;
    }
} 
```

## <a name="next-step"></a>Nächster Schritt
> [!div class="nextstepaction"]
> [Antwortaktion](conversation-designer-reply.md)

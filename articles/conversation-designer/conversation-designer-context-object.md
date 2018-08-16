---
title: API-Referenz für das Kontextobjekt | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie in Ihrem Conversation Designer-Bot auf das Kontextobjekt verweisen.
author: vkannan
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: 5ba70189c16815539524d3c9046da03ed0344b9b
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39298792"
---
# <a name="api-reference"></a>API-Referenz
> [!IMPORTANT]
> Conversation Designer ist noch nicht für alle Kunden verfügbar. Weitere Informationen zur Verfügbarkeit von Conversation Designer folgen später in diesem Jahr.

Conversation Designer ermöglicht Ihnen das Hinzufügen von benutzerdefinierter Geschäftslogik zu Ihrem Bot. Diese Skriptfunktionen werden im **Skripts**-Editor implementiert. Die Funktionen werden an verschiedenen Stellen eingebunden, z.B. in die bedingten Antwortvorlagen, die *Auslöser* oder *Aktionen* der Aufgabe oder die Dialogzustände. Alle erhalten ein `context`-Objekt vom **[IConversationContext]** in ihren Funktionsparametern.

Das folgende Codebeispiel zeigt die Signatur für eine bedingte Antwortfunktion mit dem übergebenen **Kontextobjekt**.

```javascript
/**
* @param {IConversationContext} context
*/
module.exports.NewConditionalResponse_onRun = function(context) {
    // Business logic here
    return true; // Returns a boolean
}
```

Über das `context`-Objekt können Sie auf Informationen zur Kommunikation zwischen dem Benutzer und dem Bot zugreifen.

## <a name="script-callback-functions"></a>Skript-Rückruffunktionen

Die von Ihnen erstellten benutzerdefinierten Skript-Rückruffunktionen können zahlreiche Formen annehmen. Sie können ihnen zwar andere Namen geben, funktionell nehmen sie jedoch eine der folgenden Formen an.

| Form | Parameter | Rückgabetyp | BESCHREIBUNG |
| ---- | ---- | ---- | ---- |
| Funktion vor der Antwort | context | **void** oder **promise** | Funktion, die ausgeführt wird, bevor eine Antwort gegeben wird. |
| Prozessfunktion | context | **void** oder **promise** | Funktion, die Geschäftslogik ausführt. |
| Entscheidungsfunktion | context | **string** oder **promise** | Funktion, die basierend auf Geschäftslogik Entscheidungen trifft. Die Rückgabezeichenfolge muss eine Bedingung aus einem [Entscheidungsblock](conversation-designer-dialogues.md#decision-state) erfüllen. |
| Code-Erkennungsfunktion | context | **boolean** oder **promise** | Benutzerdefinierte Geschäftslogik, die bei einem **Skriptauslöser** ausgeführt wird. Gibt `true` zurück, um eine Übereinstimmung anzugeben. Andernfalls wird `false` zurückgegeben, um den Abgleich anzubrechen. |
| onRecognize-Funktion | context | **boolean** oder **promise** | Funktion, die nur bei einer Übereinstimmung aus einer LUIS-Erkennung ausgeführt wird. Verwenden Sie diese Rückruffunktion zum Verarbeiten von LUIS-Entitäten und Zurückgeben eines entsprechenden **booleschen** Werts. Gibt `true` zurück, um eine Übereinstimmung anzugeben. Andernfalls wird `false` zurückgegeben, um den Abgleich anzubrechen. |

## <a name="iconversationcontext-interface"></a>IConversationContext-Schnittstelle

Die `IConversationContext`-Schnittstelle verfolgt Informationen zur Konversation zwischen dem Benutzer und dem Bot nach. Alle benutzerdefinierte Funktionen, die über Conversation Designer eingebunden sind, erhalten ein `context`-Objekt als Parameterargument.

## <a name="context-properties"></a>Kontexteigenschaften
Das `context`-Objekt macht die folgenden Eigenschaften verfügbar.

| NAME |  Code | BESCHREIBUNG |
| ---- | ---- | ---- |
| `request` | `context.request` | Ruft das Anforderungsobjekt ab, das die Aktivität des Bots enthält.  |
| | `context.request.attachment` | Eine Anlageaktivität, die eine adaptive Karte enthalten kann. |
| | `context.request.text` | Eine Textaktivität, die die eingehende Textnachricht vom Client enthält. |
| | `context.request.speak` | Eine Sprachaktivität, die den gesprochenen Text (fall verfügbar) vom Client enthält. |
| | `context.request.type` | Gibt den Aktivitätstyp an (Standard: „Nachricht“). |
| `responses` | `context.responses` | Verwaltet ein Array von Aktivitäten, die am Ende des aktuellen Zustands oder der CodeBehind-Ausführung an den Client zurückgesendet werden. |
| | `context.responses.push` | Fügt der Antwort eine Aktivität hinzu. |
| `global` | `context.global` | Ein JavaScript-Objekt, das von Ihnen definierte Konversationsdaten enthält. Dieses Objekt wird während der gesamten Konversation beibehalten. |
| `local` | `context.local` | Ein JavaScript-Objekt, das von Ihnen definierte Aufgabendaten enthält. Dieses Objekt wird für die Dauer einer bestimmten Aufgabe beibehalten. LUIS-Absichten werden immer an den lokalen Kontext zurückgegeben. Wenn Sie LUIS-Ergebnisse beibehalten möchten, sollten Sie sie in den `context.global`-Kontext kopieren. |
| | `context.local['@description']` | Gibt die von LUIS empfangenen unformatierten Entitäten zurück. |
| `sticky` | `context.sticky` | Gibt den Namen der aktuellen Aufgabe an |
| `currentTemplate` | `context.currentTemplate` | Ein [Vorlage für bedingte Antworten](conversation-designer-response-templates.md#conditional-response-templates), die für die Auswertung von sowohl Anzeige als auch Sprache aufgerufen wird. Dieses Objekt enthält drei Eigenschaften: <br/>1. **name**: Der Name der aktuellen Vorlage. <br/>2. **modalityDisplay**: Ein boolescher Wert, der angibt, dass die Modalität einer Anzeigeauswertung zugeordnet ist. <br/>3. **modalitySpeak**: Ein boolescher Wert, der angibt, dass die Modalität einer Sprachauswertung zugeordnet ist. |

## <a name="context-methods"></a>Kontextmethoden
Das `context`-Objekt macht die folgenden Methoden verfügbar.

| NAME | Rückgabetyp | Code | BESCHREIBUNG |
| ---- | ---- | ---- | ---- |
| `getCurrentTurn` | **Zahl** | `context.getCurrentTurn();` | Rufen Sie den „Turn“ vom Frame oben im Stapel ab, wenn Sie eine erneute Aufforderung ausführen. |

## <a name="next-step"></a>Nächster Schritt
> [!div class="nextstepaction"]
> [Erstellen eines Bots](conversation-designer-create-bot.md)

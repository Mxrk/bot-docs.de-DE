---
title: Definieren eines Dialogs als Do-Aktion | Microsoft-Dokumentation
description: Erfahren Sie, wie ein Dialog als Do-Aktion eingerichtet wird.
author: vkannan
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: d59df20821a7f63eb9ee5dea365597b1af839f75
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301568"
---
# <a name="define-a-dialogue-as-a-do-action"></a>Definieren eines Dialogs als Do-Aktion
> [!IMPORTANT]
> Conversation Designer ist noch nicht für alle Kunden verfügbar. Weitere Informationen zur Verfügbarkeit von Conversation Designer folgen später in diesem Jahr.

Ein Dialog steuert das Konversationsmodell für eine bestimmte Aufgabe. Beispiel: Ein Café-Bot, der Benutzer bei Tischreservierungen unterstützt, könnte eine Aufgabe namens „Tischreservierung“ definieren. Die **Do**-Aktion ist dann an einen Dialog gebunden, der den Konversationsfluss zwischen Bot und Benutzer modelliert. 

Dialoge sind insbesondere nützlich, wenn ein Bot ein Gespräch mit dem Benutzer führt, um eine Aufgabe auszuführen.  Benutzer geben nur selten alle erforderlichen Werte zum Abschließen einer Aufgabe in einer einzigen Äußerung an. 

Für eine Tischreservierung werden der Ort, die Anzahl der Personen, das Datum und die Uhrzeit benötigt. Ein Bot, der Tische reserviert, muss verschiedene mögliche Phrasen verstehen und verarbeiten, die der Benutzer sagen könnte. 

- *Reserviere einen Tisch*: In diesem Fall wurde keine der erforderlichen Entitäten erfasst. Der Bot muss jetzt eine Konversation mit dem Benutzer führen.
- *Reserviere einen Tisch für 19.00 Uhr diesen Samstag*: In diesem Fall sind Anliegen, Datum und Uhrzeit angegeben, der Bot muss aber noch den gewünschten Ort und die Anzahl der Personen ermitteln.

Während einer Konversation können sich einige Entitäten basierend auf der Benutzereingabe ändern. Beispiel: Wenn der Benutzer sagt „Reserviere einen Tisch in Redmond für diesen Sonntag um 19.00 Uhr“, die Gaststätte in Redmond am Sonntag jedoch um 18.00 Uhr schließt, muss der Dialog des Bots solche ungültigen Anforderungen verarbeiten. 

Conversation Designer bietet einen Drag & Drop-basierten Dialog-Designer, mit dem Sie Ihren Konversationsfluss visuell darstellen können. Der Dialog-Designer bietet sieben *Dialogzustände*, mit denen Sie Ihren Konversationsfluss modellieren können.

## <a name="dialogue-states"></a>Dialogzustände

Ein Dialog besteht aus Konversationszuständen. Der Dialog selbst wird als ein strukturierter, gesteuerter Ablauf modelliert, der die Konversationsruntime bereitstellt, eine Struktur für die Ausführung des Konversationsflusses.

Dialoge enthalten zahlreiche integrierte Zustände, die Sie verwenden können. Folgende integrierte Zustände werden unterstützt:

- [**Start**](#start-state): Stellt den Anfangszustand für einen Konversationsfluss dar. Für alle Dialoge muss mindestens ein Anfangszustand definiert sein.
- [**Rückgabe**](#return-state): Stellt den Abschluss des bestimmten Konversationsflusses dar. Angesichts der Tatsache, dass Konversationsflüsse zusammensetzbar sind, wird die Konversationsruntime bei der Rückgabe angewiesen, die Ausführung an alle möglichen Aufrufer dieses Dialogs zurückzugeben.
- [**Entscheidung**](#decision-state): Stellt einen Verzweigungspunkt im Konversationsfluss dar.
- [**Verarbeitung**](#process-state): Stellt einen Zustand dar, in dem Ihr Bot Geschäftslogik ausführt.
- [**Aufforderung**](#prompt-state): Stellt einen Zustand dar, in dem Sie den Benutzer zu einer Eingabe auffordern können. 
- [**Feedback**](#feedback-state): Stellt einen Zustand dar, in dem Sie dem Benutzer Feedback oder eine Bestätigung geben können. Beispiel: Ein Dialog, in dem bestätigt wird, dass die Tischreservierung vorgenommen wurde.
- [**Modul**](#module-state): Stellt den Aufruf eines anderen Dialogs dar. Da Dialogflüsse standardmäßig zusammensetzbar sind, kann dieser Zustand entweder einen freigegebenen Dialog oder einen anderen unter dieser Aufgabe definierten Dialog aufrufen.

Jeder Konversationszustand ist über Dialogkonnektoren im Dialog-Designer mit einem anderen Zustand verbunden.

Jedem Dialogzustand ist ein Zustandseditor zugeordnet, mit dem die Eigenschaften für diesen Zustand festgelegt werden, einschließlich der Rückruf-Funktionsnamen für benutzerdefinierte Skripts. Der **Zustandseditor** ist Bereich mit anpassbarer Größe am unteren Rand des sichtbaren Bereichs des Dialog-Designers. Zum Aufrufen des Editors doppelklicken Sie im Dialog-Designer auf einen Zustand. Daraufhin zeigt ein **Zustandseditor** die Eigenschaften für diesen Zustand an.
<!-- TODO: insert screenshot of the wrench in horizontal menu -->

Die folgenden Unterabschnitte enthalten weitere Details zu den einzelnen integrierten Dialogzuständen.

### <a name="start-state"></a>Anfangszustand

Der Anfangszustand gibt den Ausgangspunkt eines Dialogs an. Der erforderliche Wert ist der **Name** des Zustands. Der Name ist standardmäßig auf „Start“ festgelegt. Sie können ihn bearbeiten, um diesen Zustand umzubenennen.

### <a name="return-state"></a>Rückgabezustand

Der Rückgabezustand stellt den Abschluss der bestimmten Verzweigung des Konversationsflusses dar. Da Dialoge zusammensetzbar sind, weist der Zustand auch die Konversationsruntime an, die Ausführung an den Aufruferdialog zurückzugeben. Der erforderliche Wert ist der **Name** des Zustands. Der Name ist standardmäßig auf „Rückgabe“ festgelegt. Sie können ihn bearbeiten, um diesen Zustand umzubenennen.

### <a name="decision-state"></a>Entscheidungszustand

Ein Entscheidungszustand stellt einen Verzweigung im Konversationsfluss dar. Sie können ein benutzerdefiniertes Skript schreiben, um zu beurteilen, welcher Verzweigung gefolgt werden soll. Je nach Benutzereingabe und Geschäftslogik gibt das Skript einen der möglichen Übergangswerte zurück. Jeder Übergangswert fordert die Konversationsruntime zur Ausführung eines anderen Zweigs des Dialogs auf.

Erforderliche Eigenschaften für den Entscheidungszustand:
- **Name**: Eindeutiger Name für den Entscheidungszustand.
- **Bei der Ausführung auszuführender Code**: Name der Rückruffunktion, die Ihre Geschäftslogik implementiert, um zu bestimmen, welcher Zweig der Konversation gewählt wird. 

#### <a name="example-code-for-decision-state"></a>Beispielcode für den Entscheidungszustand

Das folgende Beispiel für eine Rückruffunktion gibt eine Entscheidung zurück, die der Konversationsruntime die Anweisung erteilt, welche Verzweigung ausgeführt werden soll.

```javascript
module.exports.fnDecisionState = function(context) {
    var a = context.taskEntities['a'];
    if (a[0].value === '0') {
        return 'yes';
    }
    else if (a[0].value === '1') {
        return 'no';
    }
}
```

### <a name="process-state"></a>Verarbeitungszustand

Der Verarbeitungszustand stellt einen Punkt im Dialog dar, an dem der Bot entweder die Konversation vorantreibt oder versucht, die letzte Aktion zum Abschließen der Aufgabe durchzuführen. 

Erforderliche Eigenschaften für den Verarbeitungszustand:
- **Name**: Eindeutiger Name für den Verarbeitungszustand
- **Bei der Ausführung auszuführender Code**: Name der Rückruffunktion, die Ihre Geschäftslogik implementiert.

#### <a name="example-code-for-process-state"></a>Beispielcode für den Verarbeitungszustand

Das folgende Beispiel für eine Rückruffunktion ruft das Wetter ab und gibt die Wetterdaten an den Benutzer zurück.

```javascript
module.exports.fnGetWeather = function(context) {
    var options =  {
        host: 'mock',
        path: '/get?a=b',
        method: 'get'
    };
    return http.request(options).then(function(response) {
        context.contextEntities['x'].value= response.statusCode;
        var jsonBody = JSON.parse(response.body);
          // understand response
        if (response.statusCode != "200") {
            // error
        }
    });
}
```

### <a name="prompt-state"></a>Aufforderungszustand

Der Aufforderungszustand fragt den Benutzer nach einer bestimmten Information. Aufforderungszustände stellen ein Unterdialogsystem in sich selbst dar und sind daher als komplexe Zustände definiert. 

Innerhalb eines Aufforderungszustands können Sie die tatsächliche Antwort für den Benutzer definieren und optional eine adaptive Karte einschließen. Anschließend können Sie einen Auslöser zum Analysieren und Verstehen der Antwort des Benutzers festlegen. Dieser Auslöser kann entweder LUIS oder eine benutzerdefinierte Code-Erkennung sein die einen regulären Ausdruck verwendet.  

Wenn der Benutzer eine ungültige Eingabe bereitstellt, kann der Bot die Information erneut vom Benutzer anfordern. Dieses Verhalten kann auch im Editor für den Aufforderungszustand definiert werden. 

#### <a name="prompting-the-user"></a>Auffordern des Benutzers

Für die Antwort auf eine Eingabeaufforderung können Sie die Nachricht angeben, die beim **Auffordern des Benutzers** zu einer Eingabe verwendet wird. Um beispielsweise Datum und Uhrzeit zu erfassen, sind mögliche Antworten „Wann möchten Sie kommen?“ oder „Wann möchten Sie bei uns zu Abend essen?“

#### <a name="prompt-listening-for-user-input"></a>Aufforderung wartet auf Benutzereingabe

Nachdem der Benutzer zur Antwort aufgefordert wurde, lauscht die Konversationsruntime automatisch auf Benutzereingaben und versucht, das Gesagte zu verstehen. Konfigurieren Sie einen Auslöser, der auf LUIS oder einer benutzerdefinierten Code-Erkennung basiert und versucht, die Eingabe und Absicht des Benutzers zu verstehen. Dies ist mit dem Aufgabenauslöser vergleichbar.

#### <a name="re-prompting-the-user"></a>Erneutes Auffordern des Benutzers

Im Abschnitt für erneutes Auffordern können Sie eine Antwort für jeden Versuch festlegen. Jede Zeile im Abschnitt für erneutes Auffordern entspricht der Zeichenfolge für erneutes Auffordern, die für diese bestimmte Runde verwendet wird. Die erste Antwort wird für die erste erneute Aufforderung verwendet, die zweite für die zweite und so weiter. Beispiel: 

*Ich habe leider nicht verstanden, wann Sie kommen möchten.*
*Ich kann Sie leider schlecht verstehen. Versuchen wir es noch mal: Wann möchten Sie kommen?*

#### <a name="prompt-callback-functions"></a>Rückruffunktionen für Aufforderungen

Sie können zwei verschiedene Rückruffunktionen für einen Aufforderungszustand festlegen. 

1. **Vor jeder Aufforderung und erneuten Aufforderung**: Führen Sie diese Funktion vor jeder Aufforderung oder erneuten Aufforderung aus. Diese Rückruffunktion erwartet einen booleschen Rückgabewert, in dem „true“ bedeutet, dass diese Aufforderung oder erneute Aufforderung ausgeführt werden soll, und „false“ bedeutet, dass diese Aufforderung oder erneute Aufforderung nicht ausgeführt werden soll. Mit `getTurnIndex()` können Sie den Index der aktuellen Runde für diese Aufforderungsausführung abrufen.
2. **Bei Antwort**: Führen Sie diese Funktion immer aus, nachdem eine Aufforderung generiert wurde, aber bevor diese an den Benutzer gesendet wird (einschließlich Antwort auf erneute Aufforderung). Dies ermöglicht dem Skript das Ändern der Nachricht, die an den Benutzer gesendet wird.

#### <a name="sample-code"></a>Beispielcode

Dieser Codeausschnitt zeigt ein Beispiel für den Rückruf **vor der Ausführung**.

```javascript
module.exports.fnBeforeExecuting = function(context) {
    if(context.responses[0].text === "C") {
        return false;
    }
    return true;
}
```

Dieser Codeausschnitt zeigt ein Beispiel für den Rückruf **bei der Aufforderung**.

```javascript
module.exports.fnOnPrompting = function(context) {
    // include a hint card
    var activity = context.responses.slice(-1).pop();
    activity.attachments.push({
        "contentType": "application/vnd.microsoft.card.hero",
        "content": {
            "buttons": [
                {
                    "type": "imBack",
                    "title": "1",
                    "value": "1"
                },
                {
                    "type": "imBack",
                    "title": "2",
                    "value": "2"
                }
            ]
        }
    });
}
```

Dieser Codeausschnitt zeigt ein Beispiel für den Rückruf **vor der erneuten Aufforderung**.

```javascript
module.exports.fnBeforeReprompting = function(context) {
    if(context.responses[0].text === "C") {
        return false;
    }
    return true;
}
```

### <a name="feedback-state"></a>Feedbackzustand

Verwenden Sie diesen Zustand, um dem Benutzer eine Antwort bereitzustellen. Typische Anwendungsfälle für diesen Zustand sind das endgültige Ergebnis nach dem Versuch, die Aufgabe abzuschließen, oder das Bereitstellen einer Antwort für den Benutzer in Fehlerbedingungen usw. 

Jeder Feedbackzustand erfordert einen eindeutigen Namen sowie einige mögliche Antwortwerte und kann optional Definitionen für adaptive Karten umfassen. Erfahren Sie mehr über die [Definition adaptiver Karten](conversation-designer-adaptive-cards.md).

Jeder Feedbackzustand ermöglicht außerdem eine Callback-Funktion, die **bei Antwort** ausgeführt wird. Dazu können Sie ein benutzerdefiniertes Skript schreiben, um ggf. die Nutzlast der Aktivität zu ändern, bevor sie an den Benutzer gesendet wird. 


```javascript
module.exports.fnOnResponding = function(context) {
    // include a hint card
    var activity = context.responses.slice(-1).pop();
    activity.attachments.push({
        "contentType": "application/vnd.microsoft.card.hero",
        "content": {
            "buttons": [
                {
                    "type": "imBack",
                    "title": "1",
                    "value": "1"
                },
                {
                    "type": "imBack",
                    "title": "2",
                    "value": "2"
                }
            ]
        }
    });

}
```

### <a name="module-state"></a>Modulzustand

Der Modulzustand dient zum Hinzufügen eines Verweises zum Ausführen eines Unterdialogs. Verwenden Sie diesen Zustand, um Dialoge aneinanderzureihen. 

Jeder Modulzustand erfordert einen eindeutigen Namen und einen Zeiger auf einen bestimmten auszuführenden Dialog. Mögliche Optionen für auszuführende Dialoge müssen unter den **freigegebenen Dialogen** oder unter der jeweiligen **Aufgabe** verfügbar sein.

## <a name="multiple-dialogues-under-a-task"></a>Mehrere Dialoge unter einer Aufgabe

Eine Aufgabe kann mehrere Dialoge aufweisen. Um einer Aufgabe einen Dialog hinzuzufügen, wählen Sie einfach die Aufgabe aus und klicken im linken Strukturbereich auf die Schaltfläche **Hinzufügen** und dann auf **Add dialogue** (Dialog hinzufügen). Dadurch wird unter der ausgewählten Aufgabe ein neuer Dialog hinzugefügt. 

Da Dialoge zusammensetzbar sind, können Sie den Stammdialogfluss an die Aufgabe binden, die andere Dialoge unter der Aufgabe aufruft. So können Sie untergeordnete Aufgaben kapseln und die Wiederverwendung ermöglichen. Außerdem können Sie diese Dialoge mithilfe von *Modulzuständen* zu einem Konversationsfluss verketten.

## <a name="next-step"></a>Nächster Schritt
> [!div class="nextstepaction"]
> [Antwortvorlagen](conversation-designer-response-templates.md)

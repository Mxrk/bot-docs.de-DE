---
title: Tutorial – Speichern von Benutzerstatusdaten | Microsoft-Dokumentation
description: Informationen zum Speichern von Benutzerstatusdaten im Bot Builder SDK.
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 4/23/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 86f70fd66f1bc2261339cbe0590061913b51ddbc
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42904438"
---
# <a name="save-user-state-data"></a>Speichern von Benutzerstatusdaten

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Wenn der Bot eine Benutzereingabe anfordert, möchten Sie wahrscheinlich, dass einige der Informationen in einem Speicher beibehalten werden. Mit dem Bot Builder SDK können Sie Benutzereingaben mit *In-Memory-Speicher*, *Dateispeicher* und Datenbankspeicher wie *CosmosDB* oder *SQL* speichern. 

Dieses Tutorial zeigt Ihnen, wie Sie Ihr Speicherobjekt in der Middlewareebene definieren und den Benutzer im Speicherobjekt speichern, sodass er beibehalten werden kann.

## <a name="prequisite"></a>Voraussetzung 

Dieses Tutorial baut auf dem Tutorial [Verwalten eines Konversationsflusses mit einem Wasserfall](bot-builder-tutorial-waterfall.md) auf.

## <a name="add-storage-to-middleware-layer"></a>Hinzufügen von Speicher zur Middlewareebene


## <a name="save-user-input-to-storage"></a>Speichern von Benutzereingaben im Speicher

Um die Tischreservierungskonversation zu verwalten, müssen Sie einen **Wasserfalldialog** mit vier Schritten definieren. In dieser Unterhaltung verwenden Sie außerdem eine `DatetimePrompt`- und eine `NumberPrompt`-Eingabeaufforderung zusätzlich zur `TextPrompt`-Eingabeaufforderung.

Der `reserveTable`-Dialog sieht folgendermaßen aus:

```javascript
// Reserve a table:
// Help the user to reserve a table

var reservationInfo = {
    dateTime: '',
    partySize: '',
    reserveName: ''
}

dialogs.add('reserveTable', [
    async function(dc, args, next){
        await dc.context.sendActivity("Welcome to the reservation service.");
        reservationInfo = {}; // Clears any previous data
        await dc.prompt('dateTimePrompt', "Please provide a reservation date and time.");
    },
    async function(dc, result){
        reservationInfo.dateTime = result[0].value;

        // Ask for next info
        await dc.prompt('partySizePrompt', "How many people are in your party?");
    },
    async function(dc, result){
        reservationInfo.partySize = result;

        // Ask for next info
        await dc.prompt('textPrompt', "Who's name will this be under?");
    },
    async function(dc, result){
        reservationInfo.reserveName = result;

        // Confirm reservation
        var msg = `Reservation confirmed. Reservation details: 
            <br/>Date/Time: ${reservationInfo.dateTime} 
            <br/>Party size: ${reservationInfo.partySize} 
            <br/>Reservation name: ${reservationInfo.reserveName}`;
        await dc.context.sendActivity(msg);
        await dc.end();
    }
]);

```

Nun sind Sie bereit, dies in die Botlogik einzubinden.

## <a name="start-the-dialog"></a>Starten des Dialogs

Ändern Sie die `processActivity()`-Methode Ihres Bots wie folgt:

```javascript
// Listen for incoming activity 
server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type === 'message') {
            // State will store all of your information 
            const state = conversationState.get(context);
            const dc = dialogs.createContext(context, state);

            if(context.activity.text.match(/hi/ig)){
                await dc.begin('greeting');
            }
            if(context.activity.text.match(/reserve table/ig)){
                await dc.begin('reserveTable');
            }
            else{
                // Continue executing the "current" dialog, if any.
                await dc.continue();
            }
        }
    });
});
```

Zum Zeitpunkt der Ausführung startet der Bot jedes Mal, wenn der Benutzer die Nachricht mit der Zeichenfolge `reserve table` sendet, die `reserveTable`-Unterhaltung.

## <a name="next-steps"></a>Nächste Schritte

??? 

> [!div class="nextstepaction"]
> [Speichern von Benutzerstatusdaten](bot-builder-tutorial-save-data.md)

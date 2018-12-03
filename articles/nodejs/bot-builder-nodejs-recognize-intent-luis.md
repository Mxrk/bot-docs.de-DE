---
title: Erkennen von Absichten und Entitäten mit LUIS | Microsoft-Dokumentation
description: Integrieren Sie einen Bot mit LUIS, um durch Auslösen von Dialogen mit dem Bot Builder SDK für Node.js die Absicht des Benutzers zu erkennen und entsprechend darauf zu reagieren.
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 03/28/2018
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 5df1352241485bf95a46fa981b9b16c3cb7e3925
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49998697"
---
# <a name="recognize-intents-and-entities-with-luis"></a>Erkennen von Absichten und Entitäten mit LUIS 

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

In diesem Artikel wird das Beispiel eines Bots für das Aufzeichnen von Notizen verwendet, um zu veranschaulichen, wie Language Understanding ([LUIS][LUIS]) Ihren Bot dabei unterstützt, angemessen auf Eingaben in natürlicher Sprache zu reagieren. Ein Bot erkennt, was ein Benutzer tun möchte, indem er dessen **Absicht** identifiziert. Diese Absicht wird anhand von gesprochenen oder textuellen Eingaben oder **Äußerungen** bestimmt. Die Absicht ordnet Äußerungen Aktionen zu, die der Bot ausführt (z. B. einen Dialog aufrufen). Ein Bot muss möglicherweise auch **Entitäten** extrahieren, die wichtige Wörter in Äußerungen darstellen. Entitäten sind manchmal erforderlich, um eine Absicht zu erfüllen. Im Beispiel eines Notizenbots identifiziert die Entität `Notes.Title` den Titel jeder Notiz.

## <a name="create-a-language-understanding-bot-with-bot-service"></a>Erstellen eines Language Understanding-Bots mit Bot Service

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) auf dem Menüblatt die Option **Neue Ressource erstellen** aus, und klicken Sie dann auf **Alle anzeigen**.

    ![Neue Ressource erstellen](../media/bot-builder-nodejs-use-luis/bot-service-creation.png)

2. Suchen Sie im Suchfeld nach **Web-App-Bot**. 

    ![Neue Ressource erstellen](../media/bot-builder-nodejs-use-luis/bot-service-selection.png)

3. Geben Sie auf dem Blatt **Botdienst** die erforderlichen Informationen an, und klicken Sie auf **Erstellen**. Dadurch werden der Botdienst und die LUIS-App erstellt und in Azure bereitgestellt. 
   * Legen Sie den **App-Namen** auf den Namen Ihres Bots fest. Der Name wird als Unterdomäne verwendet, wenn Ihr Bot in der Cloud bereitgestellt wird (z.B. meinnotizbot.azurewebsites.net). Dieser Name wird auch als Name der mit Ihrem Bot verknüpften LUIS-App verwendet. Kopieren Sie ihn, um später die mit dem Bot verknüpfte LUIS-App finden zu können.
   * Wählen Sie das Abonnement, die [Ressourcengruppe](/azure/azure-resource-manager/resource-group-overview), den App Service-Plan und den [Standort](https://azure.microsoft.com/en-us/regions/) aus.
   * Wählen Sie die Vorlage **Language Understanding (Node.js)** im Feld **Bot template** (Botvorlage) aus.

     ![Blatt „Bot Service“](../media/bot-builder-nodejs-use-luis/bot-service-setting-callout-template.png)

   * Aktivieren Sie das Kontrollkästchen, um den Nutzungsbedingungen des Diensts zuzustimmen.

4. Vergewissern Sie sich, dass der Botdienst bereitgestellt wurde.
    * Klicken Sie auf „Benachrichtigungen“ (Glockensymbol im oberen Bereich des Azure-Portals). Die Benachrichtigung ändert sich von **Bereitstellung gestartet** in **Bereitstellung erfolgreich**.
    * Nachdem die Benachrichtigung in **Bereitstellung erfolgreich** geändert wurde, klicken Sie auf **Zu Ressource wechseln** für diese Benachrichtigung.

## <a name="try-the-bot"></a>Testen des Bots

Vergewissern Sie sich, dass der Bot bereitgestellt wurde, indem Sie das Kontrollkästchen **Benachrichtigungen** aktivieren. Die Benachrichtigungen ändern sich von **Die Bereitstellung wird ausgeführt** in **Bereitstellung erfolgreich**. Klicken Sie auf die Schaltfläche **Zu Ressource wechseln**, um das Ressourcenblatt des Bots zu öffnen.

Klicken Sie nach der Registrierung des Bots auf **Test in Web Chat** (Im Webchat testen), um den Bereich für den Webchat zu öffnen. Geben Sie „hello“ im Webchat ein.

  ![Testen des Bots im Webchat](../media/bot-builder-nodejs-use-luis/bot-service-web-chat.png)

Der Bot antwortet mit „You have reached Greeting. You said: hello“. Dadurch wird bestätigt, dass der Bot Ihre Nachricht empfangen und an eine selbst erstellte Standard-LUIS-App übergeben hat. Diese Standard-LUIS-App erkannte die Absicht „Greeting“.

## <a name="modify-the-luis-app"></a>Ändern der LUIS-App

Melden Sie sich bei [https://www.luis.ai](https://www.luis.ai) mit dem gleichen Konto an, das Sie zum Anmelden bei Azure verwenden. Klicken Sie auf **Meine Apps**. Suchen Sie in der Liste der Apps nach der App, die mit dem Namen beginnt, den Sie beim Erstellen des Botdiensts auf dem Blatt **Botdienst** als **App-Name** angegeben haben. 

In der LUIS-App sind zunächst 4 Absichten verfügbar: „Cancel“ „Greeting“, „Help“ und „None“. <!-- picture -->

Mit den folgenden Schritten werden die Absichten „Note.Create“, „Note.ReadAloud“ und „Note.Delete“ hinzugefügt: 

1. Klicken Sie unten links auf der Seite auf **Prebuilt Domains** (Vordefinierte Domänen). Suchen Sie nach der Domäne **Note** (Notiz), und klicken Sie auf **Add domain** (Domäne hinzufügen).

2. In diesem Tutorial werden nicht alle Absichten verwendet, die in der vordefinierten Domäne **Note** (Notiz) enthalten sind. Klicken Sie auf der Seite **Intents** (Absichten) auf jeden der folgenden Absichtsnamen, und klicken Sie dann auf die Schaltfläche **Delete Intent** (Absicht löschen).
   * Note.ShowNext
   * Note.DeleteNoteItem
   * Note.Confirm
   * Note.Clear
   * Note.CheckOffItem
   * Note.AddToNote

   Es sollten nur die folgenden Absichten in der LUIS-App verbleiben: 
   * Note.ReadAloud
   * Note.Create
   * Note.Delete
   * Keine
   * Hilfe
   * Grußformel
   * Abbrechen

     ![In der LUIS-App angezeigte Absichten](../media/bot-builder-nodejs-use-luis/luis-intent-list.png)


3.  Klicken Sie in der oberen rechten Ecke auf die Schaltfläche **Train** (Trainieren), um Ihre App zu trainieren.
4.  Klicken Sie in der oberen Navigationsleiste auf **PUBLISH** (Veröffentlichen), um die Seite **Publish** (Veröffentlichen) zu öffnen. Klicken Sie auf die Schaltfläche **Publish to production slot** (In Produktionsslot veröffentlichen). Nach der erfolgreichen Veröffentlichung wird unter der URL, die auf der Seite **Publish App** (App veröffentlichen) in der Spalte **Endpoint** (Endpunkt) und in der Zeile, die mit dem Ressourcennamen „Starter_Key“ beginnt, angezeigt wird, eine LUIS-App bereitgestellt. Die URL weist ein ähnliches Format wie im folgenden Beispiel auf: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx?subscription-key=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx&timezoneOffset=0&verbose=true&q=`. Die App-ID und der Abonnementschlüssel in dieser URL sind mit LuisAppId und LuisAPIKey unter ** App Service Settings > "ApplicationSettings" > App settings ** identisch.


## <a name="modify-the-bot-code"></a>Ändern des Botcodes

Klicken Sie auf **Erstellen**, und klicken Sie dann auf **Open online code editor**  (Onlinecode-Editor öffnen).

   ![Open online code editor (Onlinecode-Editor öffnen)](../media/bot-builder-nodejs-use-luis/bot-service-build.png)

Öffnen Sie im Code-Editor `app.js`. Diese Datei enthält den folgenden Code:

```javascript
var restify = require('restify');
var builder = require('botbuilder');
var botbuilder_azure = require("botbuilder-azure");

// Setup Restify Server
var server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
   console.log('%s listening to %s', server.name, server.url); 
});
  
// Create chat connector for communicating with the Bot Framework Service
var connector = new builder.ChatConnector({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword,
    openIdMetadata: process.env.BotOpenIdMetadata 
});

// Listen for messages from users 
server.post('/api/messages', connector.listen());

/*----------------------------------------------------------------------------------------
* Bot Storage: This is a great spot to register the private state storage for your bot. 
* We provide adapters for Azure Table, CosmosDb, SQL Azure, or you can implement your own!
* For samples and documentation, see: https://github.com/Microsoft/BotBuilder-Azure
* ---------------------------------------------------------------------------------------- */

var tableName = 'botdata';
var azureTableClient = new botbuilder_azure.AzureTableClient(tableName, process.env['AzureWebJobsStorage']);
var tableStorage = new botbuilder_azure.AzureBotStorage({ gzipData: false }, azureTableClient);

// Create your bot with a function to receive messages from the user
// This default message handler is invoked if the user's utterance doesn't
// match any intents handled by other dialogs.
var bot = new builder.UniversalBot(connector, function (session, args) {
    session.send('You reached the default message handler. You said \'%s\'.', session.message.text);
});

bot.set('storage', tableStorage);

// Make sure you add code to validate these fields
var luisAppId = process.env.LuisAppId;
var luisAPIKey = process.env.LuisAPIKey;
var luisAPIHostName = process.env.LuisAPIHostName || 'westus.api.cognitive.microsoft.com';

const LuisModelUrl = 'https://' + luisAPIHostName + '/luis/v2.0/apps/' + luisAppId + '?subscription-key=' + luisAPIKey;

// Create a recognizer that gets intents from LUIS, and add it to the bot
var recognizer = new builder.LuisRecognizer(LuisModelUrl);
bot.recognizer(recognizer);

// Add a dialog for each intent that the LUIS app recognizes.
// See https://docs.microsoft.com/en-us/bot-framework/nodejs/bot-builder-nodejs-recognize-intent-luis 
bot.dialog('GreetingDialog',
    (session) => {
        session.send('You reached the Greeting intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Greeting'
})

bot.dialog('HelpDialog',
    (session) => {
        session.send('You reached the Help intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Help'
})

bot.dialog('CancelDialog',
    (session) => {
        session.send('You reached the Cancel intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Cancel'
})

```


> [!TIP] 
> Den in diesem Artikel beschriebenen Beispielcode finden Sie auch im [Beispiel für den Notizbot][NotesSample].



## <a name="edit-the-default-message-handler"></a>Bearbeiten des Standardnachrichtenhandlers
Der Bot verfügt über einen Standardnachrichtenhandler. Bearbeiten Sie ihn so, dass er folgenden Werten entspricht: 
```javascript
// Create your bot with a function to receive messages from the user.
// This default message handler is invoked if the user's utterance doesn't
// match any intents handled by other dialogs.
var bot = new builder.UniversalBot(connector, function (session, args) {
    session.send("Hi... I'm the note bot sample. I can create new notes, read saved notes to you and delete notes.");

   // If the object for storing notes in session.userData doesn't exist yet, initialize it
   if (!session.userData.notes) {
       session.userData.notes = {};
       console.log("initializing userData.notes in default message handler");
   }
});
```

## <a name="handle-the-notecreate-intent"></a>Verarbeiten der Absicht „Note.Create“

Kopieren Sie den folgenden Code, und fügen Sie ihn am Ende von "app.js" ein:

```javascript
// CreateNote dialog
bot.dialog('CreateNote', [
    function (session, args, next) {
        // Resolve and store any Note.Title entity passed from LUIS.
        var intent = args.intent;
        var title = builder.EntityRecognizer.findEntity(intent.entities, 'Note.Title');

        var note = session.dialogData.note = {
          title: title ? title.entity : null,
        };
        
        // Prompt for title
        if (!note.title) {
            builder.Prompts.text(session, 'What would you like to call your note?');
        } else {
            next();
        }
    },
    function (session, results, next) {
        var note = session.dialogData.note;
        if (results.response) {
            note.title = results.response;
        }

        // Prompt for the text of the note
        if (!note.text) {
            builder.Prompts.text(session, 'What would you like to say in your note?');
        } else {
            next();
        }
    },
    function (session, results) {
        var note = session.dialogData.note;
        if (results.response) {
            note.text = results.response;
        }
        
        // If the object for storing notes in session.userData doesn't exist yet, initialize it
        if (!session.userData.notes) {
            session.userData.notes = {};
            console.log("initializing session.userData.notes in CreateNote dialog");
        }
        // Save notes in the notes object
        session.userData.notes[note.title] = note;

        // Send confirmation to user
        session.endDialog('Creating note named "%s" with text "%s"',
            note.title, note.text);
    }
]).triggerAction({ 
    matches: 'Note.Create',
    confirmPrompt: "This will cancel the creation of the note you started. Are you sure?" 
}).cancelAction('cancelCreateNote', "Note canceled.", {
    matches: /^(cancel|nevermind)/i,
    confirmPrompt: "Are you sure?"
});
```

Alle Entitäten in der Äußerung werden mithilfe des `args`-Parameters an den Dialog übergeben. Mit dem ersten Schritt im [Wasserfall][waterfall] wird [EntityRecognizer.findEntity][EntityRecognizer_findEntity] aufgerufen, um von einer der `Note.Title`-Entitäten in der LUIS-Antwort den Titel der Notiz abzurufen. Wenn die LUIS-App keine `Note.Title`-Entität erkannt hat, fordert der Bot den Benutzer auf, den Namen der Notiz anzugeben. Im zweiten Schritt des Wasserfalls wird der Text angefordert, der in der Notiz enthalten sein soll. Sobald der Bot über den Text der Notiz verfügt, wird im dritten Schritt [session.userData][session_userData] verwendet, um die Notiz in einem `notes`-Objekt zu speichern. Dabei wird der Titel als Schlüssel verwendet. Weitere Informationen zu `session.UserData` finden Sie unter [Verwalten von Statusdaten](./bot-builder-nodejs-state.md). 



## <a name="handle-the-notedelete-intent"></a>Verarbeiten der Absicht „Note.Delete“
Ebenso wie bei der Absicht `Note.Create` überprüft der Bot den `args`-Parameter, ob dieser einen Titel enthält. Wenn kein Titel erkannt wird, fordert der Bot den Benutzer zur Eingabe auf. Mithilfe des Titels wird die Notiz gesucht, die aus `session.userData.notes` gelöscht werden soll. 



Kopieren Sie den folgenden Code, und fügen Sie ihn am Ende von "app.js" ein:
```javascript
// Delete note dialog
bot.dialog('DeleteNote', [
    function (session, args, next) {
        if (noteCount(session.userData.notes) > 0) {
            // Resolve and store any Note.Title entity passed from LUIS.
            var title;
            var intent = args.intent;
            var entity = builder.EntityRecognizer.findEntity(intent.entities, 'Note.Title');
            if (entity) {
                // Verify that the title is in our set of notes.
                title = builder.EntityRecognizer.findBestMatch(session.userData.notes, entity.entity);
            }
            
            // Prompt for note name
            if (!title) {
                builder.Prompts.choice(session, 'Which note would you like to delete?', session.userData.notes);
            } else {
                next({ response: title });
            }
        } else {
            session.endDialog("No notes to delete.");
        }
    },
    function (session, results) {
        delete session.userData.notes[results.response.entity];        
        session.endDialog("Deleted the '%s' note.", results.response.entity);
    }
]).triggerAction({
    matches: 'Note.Delete'
}).cancelAction('cancelDeleteNote', "Ok - canceled note deletion.", {
    matches: /^(cancel|nevermind)/i
});
```

Der Code, der `Note.Delete` verarbeitet, bestimmt mithilfe der Funktion `noteCount`, ob das `notes`-Objekt Notizen enthält. 

Fügen Sie am Ende von `app.js` die `noteCount`-Hilfsfunktion ein.

[!code-js[Add a helper function that returns the number of notes (JavaScript)](../includes/code/node-basicNote.js#CountNotesHelper)]

## <a name="handle-the-notereadaloud-intent"></a>Verarbeiten der Absicht „Note.ReadAloud“

Kopieren Sie den folgenden Code, und fügen Sie ihn nach dem Handler für `Note.Delete` in `app.js` ein:

```javascript
// Read note dialog
bot.dialog('ReadNote', [
    function (session, args, next) {
        if (noteCount(session.userData.notes) > 0) {
           
            // Resolve and store any Note.Title entity passed from LUIS.
            var title;
            var intent = args.intent;
            var entity = builder.EntityRecognizer.findEntity(intent.entities, 'Note.Title');
            if (entity) {
                // Verify it's in our set of notes.
                title = builder.EntityRecognizer.findBestMatch(session.userData.notes, entity.entity);
            }
            
            // Prompt for note name
            if (!title) {
                builder.Prompts.choice(session, 'Which note would you like to read?', session.userData.notes);
            } else {
                next({ response: title });
            }
        } else {
            session.endDialog("No notes to read.");
        }
    },
    function (session, results) {        
        session.endDialog("Here's the '%s' note: '%s'.", results.response.entity, session.userData.notes[results.response.entity].text);
    }
]).triggerAction({
    matches: 'Note.ReadAloud'
}).cancelAction('cancelReadNote', "Ok.", {
    matches: /^(cancel|nevermind)/i
});

```

Das `session.userData.notes`-Objekt wird als drittes Argument an `builder.Prompts.choice` übergeben, sodass die Eingabeaufforderung dem Benutzer eine Liste mit Notizen anzeigt.

Nachdem Sie nun Handler für die neuen Absichten hinzugefügt haben, enthält der vollständige Code für `app.js` Folgendes:

```javascript
var restify = require('restify');
var builder = require('botbuilder');
var botbuilder_azure = require("botbuilder-azure");

// Setup Restify Server
var server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
   console.log('%s listening to %s', server.name, server.url); 
});
  
// Create chat connector for communicating with the Bot Framework Service
var connector = new builder.ChatConnector({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword,
    openIdMetadata: process.env.BotOpenIdMetadata 
});

// Listen for messages from users 
server.post('/api/messages', connector.listen());

/*----------------------------------------------------------------------------------------
* Bot Storage: This is a great spot to register the private state storage for your bot. 
* We provide adapters for Azure Table, CosmosDb, SQL Azure, or you can implement your own!
* For samples and documentation, see: https://github.com/Microsoft/BotBuilder-Azure
* ---------------------------------------------------------------------------------------- */

var tableName = 'botdata';
var azureTableClient = new botbuilder_azure.AzureTableClient(tableName, process.env['AzureWebJobsStorage']);
var tableStorage = new botbuilder_azure.AzureBotStorage({ gzipData: false }, azureTableClient);

// Create your bot with a function to receive messages from the user.
// This default message handler is invoked if the user's utterance doesn't
// match any intents handled by other dialogs.
var bot = new builder.UniversalBot(connector, function (session, args) {
    session.send("Hi... I'm the note bot sample. I can create new notes, read saved notes to you and delete notes.");

   // If the object for storing notes in session.userData doesn't exist yet, initialize it
   if (!session.userData.notes) {
       session.userData.notes = {};
       console.log("initializing userData.notes in default message handler");
   }
});

bot.set('storage', tableStorage);

// Make sure you add code to validate these fields
var luisAppId = process.env.LuisAppId;
var luisAPIKey = process.env.LuisAPIKey;
var luisAPIHostName = process.env.LuisAPIHostName || 'westus.api.cognitive.microsoft.com';

const LuisModelUrl = 'https://' + luisAPIHostName + '/luis/v2.0/apps/' + luisAppId + '?subscription-key=' + luisAPIKey;

// Create a recognizer that gets intents from LUIS, and add it to the bot
var recognizer = new builder.LuisRecognizer(LuisModelUrl);
bot.recognizer(recognizer);

// CreateNote dialog
bot.dialog('CreateNote', [
    function (session, args, next) {
        // Resolve and store any Note.Title entity passed from LUIS.
        var intent = args.intent;
        var title = builder.EntityRecognizer.findEntity(intent.entities, 'Note.Title');

        var note = session.dialogData.note = {
          title: title ? title.entity : null,
        };
        
        // Prompt for title
        if (!note.title) {
            builder.Prompts.text(session, 'What would you like to call your note?');
        } else {
            next();
        }
    },
    function (session, results, next) {
        var note = session.dialogData.note;
        if (results.response) {
            note.title = results.response;
        }

        // Prompt for the text of the note
        if (!note.text) {
            builder.Prompts.text(session, 'What would you like to say in your note?');
        } else {
            next();
        }
    },
    function (session, results) {
        var note = session.dialogData.note;
        if (results.response) {
            note.text = results.response;
        }
        
        // If the object for storing notes in session.userData doesn't exist yet, initialize it
        if (!session.userData.notes) {
            session.userData.notes = {};
            console.log("initializing session.userData.notes in CreateNote dialog");
        }
        // Save notes in the notes object
        session.userData.notes[note.title] = note;

        // Send confirmation to user
        session.endDialog('Creating note named "%s" with text "%s"',
            note.title, note.text);
    }
]).triggerAction({ 
    matches: 'Note.Create',
    confirmPrompt: "This will cancel the creation of the note you started. Are you sure?" 
}).cancelAction('cancelCreateNote', "Note canceled.", {
    matches: /^(cancel|nevermind)/i,
    confirmPrompt: "Are you sure?"
});

// Delete note dialog
bot.dialog('DeleteNote', [
    function (session, args, next) {
        if (noteCount(session.userData.notes) > 0) {
            // Resolve and store any Note.Title entity passed from LUIS.
            var title;
            var intent = args.intent;
            var entity = builder.EntityRecognizer.findEntity(intent.entities, 'Note.Title');
            if (entity) {
                // Verify that the title is in our set of notes.
                title = builder.EntityRecognizer.findBestMatch(session.userData.notes, entity.entity);
            }
            
            // Prompt for note name
            if (!title) {
                builder.Prompts.choice(session, 'Which note would you like to delete?', session.userData.notes);
            } else {
                next({ response: title });
            }
        } else {
            session.endDialog("No notes to delete.");
        }
    },
    function (session, results) {
        delete session.userData.notes[results.response.entity];        
        session.endDialog("Deleted the '%s' note.", results.response.entity);
    }
]).triggerAction({
    matches: 'Note.Delete'
}).cancelAction('cancelDeleteNote', "Ok - canceled note deletion.", {
    matches: /^(cancel|nevermind)/i
});


// Read note dialog
bot.dialog('ReadNote', [
    function (session, args, next) {
        if (noteCount(session.userData.notes) > 0) {
           
            // Resolve and store any Note.Title entity passed from LUIS.
            var title;
            var intent = args.intent;
            var entity = builder.EntityRecognizer.findEntity(intent.entities, 'Note.Title');
            if (entity) {
                // Verify it's in our set of notes.
                title = builder.EntityRecognizer.findBestMatch(session.userData.notes, entity.entity);
            }
            
            // Prompt for note name
            if (!title) {
                builder.Prompts.choice(session, 'Which note would you like to read?', session.userData.notes);
            } else {
                next({ response: title });
            }
        } else {
            session.endDialog("No notes to read.");
        }
    },
    function (session, results) {        
        session.endDialog("Here's the '%s' note: '%s'.", results.response.entity, session.userData.notes[results.response.entity].text);
    }
]).triggerAction({
    matches: 'Note.ReadAloud'
}).cancelAction('cancelReadNote', "Ok.", {
    matches: /^(cancel|nevermind)/i
});


// Helper function to count the number of notes stored in session.userData.notes
function noteCount(notes) {

    var i = 0;
    for (var name in notes) {
        i++;
    }
    return i;
}
```

## <a name="test-the-bot"></a>Testen des Bots

Klicken Sie im Azure-Portal auf **In Webchat testen**, um den Bot zu testen. Probieren Sie, Nachrichten wie „Create a note“ (Notiz erstellen), „Read my notes“ (Meine Notizen lesen) und „delete notes“ (Notizen löschen) einzugeben, um die Absichten aufzurufen, die Sie hinzugefügt haben.
   ![Testen des Notizenbots im Webchat](../media/bot-builder-nodejs-use-luis/bot-service-test-notebot.png)

> [!TIP]
> Wenn Sie feststellen, dass Ihr Bot nicht immer die richtige Absicht bzw. die richtigen Entitäten erkennt, verbessern Sie die Leistung Ihrer LUIS-App, indem Sie sie mit weiteren Beispieläußerungen trainieren. Sie können Ihre LUIS-App erneut trainieren, ohne Änderungen am Code Ihres Bots vorzunehmen. Weitere Informationen finden Sie unter [Hinzufügen von Beispieläußerungen](/azure/cognitive-services/LUIS/add-example-utterances) und [Trainieren und Testen der LUIS-App](/azure/cognitive-services/LUIS/train-test).


## <a name="next-steps"></a>Nächste Schritte

Beim Testen des Bots können Sie sehen, dass die Erkennung eine Unterbrechung des derzeit aktiven Dialogs auslösen kann. Das Zulassen und Verarbeiten von Unterbrechungen ist ein flexibles Konzept, das die tatsächlichen Aktivitäten der Benutzer berücksichtigt. Erfahren Sie mehr über die verschiedenen Aktionen, die Sie einer erkannten Absicht zuordnen können.

> [!div class="nextstepaction"]
> [Behandeln von Benutzeraktionen](bot-builder-nodejs-dialog-actions.md)


[LUIS]: https://www.luis.ai/

[intentDialog]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html

[intentDialog_matches]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html#matches 

[NotesSample]: https://github.com/Microsoft/BotFramework-Samples/tree/master/docs-samples/Node/basics-naturalLanguage

[triggerAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#triggeraction

[confirmPrompt]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#confirmprompt

[waterfall]: bot-builder-nodejs-dialog-manage-conversation-flow.md#manage-conversation-flow-with-a-waterfall

[session_userData]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session.html#userdata

[EntityRecognizer_findEntity]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.entityrecognizer.html#findentity

[matches]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#matches

[LUISAzureDocs]: /azure/cognitive-services/LUIS/Home

[Dialog]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html

[IntentRecognizerSetOptions]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.iintentrecognizersetoptions.html

[LuisRecognizer]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.luisrecognizer

[LUISConcepts]: https://docs.botframework.com/en-us/node/builder/guides/understanding-natural-language/

[DisambiguationSample]: https://aka.ms/v3-js-onDisambiguateRoute

[IDisambiguateRouteHandler]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.idisambiguateroutehandler.html

[RegExpRecognizer]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.regexprecognizer.html

[AlarmBot]: https://aka.ms/v3-js-luisSample

[UniversalBot]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.universalbot.html

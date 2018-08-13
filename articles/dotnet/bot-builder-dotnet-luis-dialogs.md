---
title: Erkennen von Absichten und Entitäten mit LUIS | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihrem Bot mithilfe der LUIS-Dialoge im Bot Builder SDK für .NET das Verstehen natürlicher Sprache ermöglichen.
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: b98eea6bdae097beec85e93301e5380a1de991c3
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39303240"
---
# <a name="recognize-intents-and-entities-with-luis"></a>Erkennen von Absichten und Entitäten mit LUIS 

In diesem Artikel wird das Beispiel eines Bots für das Aufzeichnen von Notizen verwendet, um zu veranschaulichen, wie Language Understanding ([LUIS][LUIS]) Ihren Bot dabei unterstützt, angemessen auf Eingaben in natürlicher Sprache zu reagieren. Ein Bot erkennt, was ein Benutzer tun möchte, indem er dessen **Absicht** identifiziert. Diese Absicht wird anhand von gesprochenen oder textuellen Eingaben oder **Äußerungen** bestimmt. Die Absicht ordnet Äußerungen Aktionen zu, die der Bot ausführt. Ein Notizenbot erkennt z. B. die Absicht `Notes.Create`, um die Funktion zum Erstellen einer Notiz aufzurufen. Ein Bot muss möglicherweise auch **Entitäten** extrahieren, die wichtige Wörter in Äußerungen darstellen. Im Beispiel eines Notizenbots identifiziert die Entität `Notes.Title` den Titel jeder Notiz.

## <a name="create-a-language-understanding-bot-with-bot-service"></a>Erstellen eines Language Understanding-Bots mit Bot Service

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) auf dem Menüblatt die Option **Neue Ressource erstellen** aus, und klicken Sie dann auf **Alle anzeigen**.<!-- Start with the steps in [Create a bot with Bot Service](../bot-service-quickstart.md) to start creating a new bot service.  -->

    ![Neue Ressource erstellen](../media/bot-builder-dotnet-use-luis/bot-service-creation.png)

2. Suchen Sie im Suchfeld nach **Web-App-Bot**. 

    ![Neue Ressource erstellen](../media/bot-builder-dotnet-use-luis/bot-service-selection.png)

3. Geben Sie auf dem Blatt **Botdienst** die erforderlichen Informationen an, und klicken Sie auf **Erstellen**. Dadurch werden der Botdienst und die LUIS-App erstellt und in Azure bereitgestellt. 
   * Legen Sie den **App-Namen** auf den Namen Ihres Bots fest. Der Name wird als Unterdomäne verwendet, wenn Ihr Bot in der Cloud bereitgestellt wird (z.B. meinnotizbot.azurewebsites.net). Dieser Name wird auch als Name der mit Ihrem Bot verknüpften LUIS-App verwendet. Kopieren Sie ihn, um später die mit dem Bot verknüpfte LUIS-App finden zu können.
   * Wählen Sie das Abonnement, die [Ressourcengruppe](/azure/azure-resource-manager/resource-group-overview), den App Service-Plan und den [Standort](https://azure.microsoft.com/en-us/regions/) aus.
   * Wählen Sie die Vorlage **Language Understanding (C#)** im Feld **Bot template** (Botvorlage) aus.

     ![Blatt „Bot Service“](../media/bot-builder-dotnet-use-luis/bot-service-setting-callout-template.png)

   * Aktivieren Sie das Kontrollkästchen, um den Nutzungsbedingungen des Diensts zuzustimmen.

4. Vergewissern Sie sich, dass der Botdienst bereitgestellt wurde.
    * Klicken Sie auf „Benachrichtigungen“ (Glockensymbol im oberen Bereich des Azure-Portals). Die Benachrichtigung ändert sich von **Bereitstellung gestartet** in **Bereitstellung erfolgreich**.
    * Nachdem die Benachrichtigung in **Bereitstellung erfolgreich** geändert wurde, klicken Sie auf **Zu Ressource wechseln** für diese Benachrichtigung.

## <a name="try-the-bot"></a>Testen des Bots

Vergewissern Sie sich, dass der Bot bereitgestellt wurde, indem Sie das Kontrollkästchen **Benachrichtigungen** aktivieren. Die Benachrichtigungen ändern sich von **Die Bereitstellung wird ausgeführt** in **Bereitstellung erfolgreich**. Klicken Sie auf die Schaltfläche **Zu Ressource wechseln**, um das Ressourcenblatt des Bots zu öffnen.

Klicken Sie nach der Registrierung des Bots auf **Test in Web Chat** (Im Webchat testen), um den Bereich für den Webchat zu öffnen. Geben Sie „hello“ im Webchat ein.

  ![Testen des Bots im Webchat](../media/bot-builder-dotnet-use-luis/bot-service-web-chat.png)

Der Bot antwortet mit „You have reached Greeting. You said: hello“. Dadurch wird bestätigt, dass der Bot Ihre Nachricht empfangen und an eine selbst erstellte Standard-LUIS-App übergeben hat. Diese Standard-LUIS-App erkannte die Absicht „Greeting“.

## <a name="modify-the-luis-app"></a>Ändern der LUIS-App

Melden Sie sich bei [https://www.luis.ai](https://www.luis.ai) mit dem gleichen Konto an, das Sie zum Anmelden bei Azure verwenden. Klicken Sie auf **Meine Apps**. Suchen Sie in der Liste der Apps nach der App, die mit dem Namen beginnt, den Sie beim Erstellen des Botdiensts auf dem Blatt **Botdienst** als **App-Name** angegeben haben. 

In der LUIS-App sind zunächst 4 Absichten verfügbar: „Cancel“ „Greeting“, „Help“ und „None“. <!-- picture -->

Mit den folgenden Schritten werden die Absichten „Note.Create“, „Note.ReadAloud“ und „Note.Delete“ hinzugefügt: 

1. Klicken Sie unten links auf der Seite auf **Prebuit Domains** (Vordefinierte Domänen). Suchen Sie nach der Domäne **Note** (Notiz), und klicken Sie auf **Add domain** (Domäne hinzufügen).

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

     ![In der LUIS-App angezeigte Absichten](../media/bot-builder-dotnet-use-luis/luis-intent-list.png)

3. Klicken Sie in der oberen rechten Ecke auf die Schaltfläche **Train** (Trainieren), um Ihre App zu trainieren.
4. Klicken Sie in der oberen Navigationsleiste auf **PUBLISH** (Veröffentlichen), um die Seite **Publish** (Veröffentlichen) zu öffnen. Klicken Sie auf die Schaltfläche **Publish to production slot** (In Produktionsslot veröffentlichen). Kopieren Sie nach der erfolgreichen Veröffentlichung die auf der Seite **Publish App** (App veröffentlichen) in der Spalte **Endpoint** (Endpunkt) angezeigte URL in die Zeile, die mit dem Ressourcennamen „Starter_Key“ beginnt. Speichern Sie diese URL zur späteren Verwendung im Code Ihres Bots. Die URL weist ein ähnliches Format wie im folgenden Beispiel auf: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx?subscription-key=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx&timezoneOffset=0&verbose=true&q=`

## <a name="modify-the-bot-code"></a>Ändern des Botcodes

Klicken Sie auf **Erstellen**, und klicken Sie dann auf **Open online code editor ** (Onlinecode-Editor öffnen).
    ![Open online code editor](../media/bot-builder-dotnet-use-luis/bot-service-build.png) (Onlinecode-Editor öffnen)

Öffnen Sie im Code-Editor `BasicLuisDialog.cs`. Darin ist der folgende Code zum Verarbeiten von Absichten aus der LUIS-App enthalten.
```cs
using System;
using System.Configuration;
using System.Threading.Tasks;

using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Builder.Luis;
using Microsoft.Bot.Builder.Luis.Models;

namespace Microsoft.Bot.Sample.LuisBot
{
    // For more information about this template visit http://aka.ms/azurebots-csharp-luis
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        public BasicLuisDialog() : base(new LuisService(new LuisModelAttribute(
            ConfigurationManager.AppSettings["LuisAppId"], 
            ConfigurationManager.AppSettings["LuisAPIKey"], 
            domain: ConfigurationManager.AppSettings["LuisAPIHostName"])))
        {
        }

        [LuisIntent("None")]
        public async Task NoneIntent(IDialogContext context, LuisResult result)
        {
            await this.ShowLuisResult(context, result);
        }

        // Go to https://luis.ai and create a new intent, then train/publish your luis app.
        // Finally replace "Greeting" with the name of your newly created intent in the following handler
        [LuisIntent("Greeting")]
        public async Task GreetingIntent(IDialogContext context, LuisResult result)
        {
            await this.ShowLuisResult(context, result);
        }

        [LuisIntent("Cancel")]
        public async Task CancelIntent(IDialogContext context, LuisResult result)
        {
            await this.ShowLuisResult(context, result);
        }

        [LuisIntent("Help")]
        public async Task HelpIntent(IDialogContext context, LuisResult result)
        {
            await this.ShowLuisResult(context, result);
        }

        private async Task ShowLuisResult(IDialogContext context, LuisResult result) 
        {
            await context.PostAsync($"You have reached {result.Intents[0].Intent}. You said: {result.Query}");
            context.Wait(MessageReceived);
        }
    }
}
```
### <a name="create-a-class-for-storing-notes"></a>Erstellen einer Klasse zum Speichern von Notizen

Fügen Sie in der Datei „BasicLuisDialog.cs“ die folgende `using`-Anweisung hinzu.

```cs
using System.Collections.Generic;
```

Fügen Sie den folgenden Code in der `BasicLuisDialog`-Klasse nach der Konstruktordefinition hinzu.

```cs
        // Store notes in a dictionary that uses the title as a key
        private readonly Dictionary<string, Note> noteByTitle = new Dictionary<string, Note>();
        
        [Serializable]
        public sealed class Note : IEquatable<Note>
        {

            public string Title { get; set; }
            public string Text { get; set; }

            public override string ToString()
            {
                return $"[{this.Title} : {this.Text}]";
            }

            public bool Equals(Note other)
            {
                return other != null
                    && this.Text == other.Text
                    && this.Title == other.Title;
            }

            public override bool Equals(object other)
            {
                return Equals(other as Note);
            }

            public override int GetHashCode()
            {
                return this.Title.GetHashCode();
            }
        }

        // CONSTANTS        
        // Name of note title entity
        public const string Entity_Note_Title = "Note.Title";
        // Default note title
        public const string DefaultNoteTitle = "default";
```

### <a name="handle-the-notecreate-intent"></a>Verarbeiten der Absicht „Note.Create“
Fügen Sie der `BasicLuisDialog`-Klasse den folgenden Code hinzu, um die Absicht „Note.Create“ zu verarbeiten.

```cs
        private Note noteToCreate;
        private string currentTitle;
        [LuisIntent("Note.Create")]
        public Task NoteCreateIntent(IDialogContext context, LuisResult result)
        {
            EntityRecommendation title;
            if (!result.TryFindEntity(Entity_Note_Title, out title))
            {
                // Prompt the user for a note title
                PromptDialog.Text(context, After_TitlePrompt, "What is the title of the note you want to create?");
            }
            else
            {
                var note = new Note() { Title = title.Entity };
                noteToCreate = this.noteByTitle[note.Title] = note;

                // Prompt the user for what they want to say in the note           
                PromptDialog.Text(context, After_TextPrompt, "What do you want to say in your note?");
            }
            
            return Task.CompletedTask;
        }
        
        
        private async Task After_TitlePrompt(IDialogContext context, IAwaitable<string> result)
        {
            EntityRecommendation title;
            // Set the title (used for creation, deletion, and reading)
            currentTitle = await result;
            if (currentTitle != null)
            {
                title = new EntityRecommendation(type: Entity_Note_Title) { Entity = currentTitle };
            }
            else
            {
                // Use the default note title
                title = new EntityRecommendation(type: Entity_Note_Title) { Entity = DefaultNoteTitle };
            }

            // Create a new note object 
            var note = new Note() { Title = title.Entity };
            // Add the new note to the list of notes and also save it in order to add text to it later
            noteToCreate = this.noteByTitle[note.Title] = note;

            // Prompt the user for what they want to say in the note           
            PromptDialog.Text(context, After_TextPrompt, "What do you want to say in your note?");

        }

        private async Task After_TextPrompt(IDialogContext context, IAwaitable<string> result)
        {
            // Set the text of the note
            noteToCreate.Text = await result;
            
            await context.PostAsync($"Created note **{this.noteToCreate.Title}** that says \"{this.noteToCreate.Text}\".");
            
            context.Wait(MessageReceived);
        }
```

### <a name="handle-the-notereadaloud-intent"></a>Verarbeiten der Absicht „Note.ReadAloud“
Der Bot kann die Absicht `Note.ReadAloud` verwenden, um den Inhalt einer Notiz anzuzeigen oder den Inhalt aller Notizen, wenn der Titel der Notiz nicht erkannt wird.

Fügen Sie den folgenden Code in die `BasicLuisDialog`-Klasse ein.
```cs
        [LuisIntent("Note.ReadAloud")]
        public async Task NoteReadAloudIntent(IDialogContext context, LuisResult result)
        {
            Note note;
            if (TryFindNote(result, out note))
            {
                await context.PostAsync($"**{note.Title}**: {note.Text}.");
            }
            else
            {
                // Print out all the notes if no specific note name was detected
                string NoteList = "Here's the list of all notes: \n\n";
                foreach (KeyValuePair<string, Note> entry in noteByTitle)
                {
                    Note noteInList = entry.Value;
                    NoteList += $"**{noteInList.Title}**: {noteInList.Text}.\n\n";
                }
                await context.PostAsync(NoteList);
            }

            context.Wait(MessageReceived);
        }
        
        public bool TryFindNote(string noteTitle, out Note note)
        {
            bool foundNote = this.noteByTitle.TryGetValue(noteTitle, out note); // TryGetValue returns false if no match is found.
            return foundNote;
        }
        
        public bool TryFindNote(LuisResult result, out Note note)
        {
            note = null;

            string titleToFind;

            EntityRecommendation title;
            if (result.TryFindEntity(Entity_Note_Title, out title))
            {
                titleToFind = title.Entity;
            }
            else
            {
                titleToFind = DefaultNoteTitle;
            }

            return this.noteByTitle.TryGetValue(titleToFind, out note); // TryGetValue returns false if no match is found.
        }
```

### <a name="handle-the-notedelete-intent"></a>Verarbeiten der Absicht „Note.Delete“
Fügen Sie den folgenden Code in die `BasicLuisDialog`-Klasse ein.

```cs
        [LuisIntent("Note.Delete")]
        public async Task NoteDeleteIntent(IDialogContext context, LuisResult result)
        {
            Note note;
            if (TryFindNote(result, out note))
            {
                this.noteByTitle.Remove(note.Title);
                await context.PostAsync($"Note {note.Title} deleted");
            }
            else
            {                             
                // Prompt the user for a note title
                PromptDialog.Text(context, After_DeleteTitlePrompt, "What is the title of the note you want to delete?");                         
            }           
        }

        private async Task After_DeleteTitlePrompt(IDialogContext context, IAwaitable<string> result)
        {
            Note note;
            string titleToDelete = await result;
            bool foundNote = this.noteByTitle.TryGetValue(titleToDelete, out note);

            if (foundNote)
            {
                this.noteByTitle.Remove(note.Title);
                await context.PostAsync($"Note {note.Title} deleted");
            }
            else
            {
                await context.PostAsync($"Did not find note named {titleToDelete}.");
            }

            context.Wait(MessageReceived);
        }
```

## <a name="build-the-bot"></a>Erstellen des Bots
Klicken Sie im Code-Editor mit der rechten Maustaste auf **build.cmd**, und wählen Sie **Run from Console** (In Konsole ausführen) aus.

   ![„build.cmd“ ausführen](../media/bot-builder-dotnet-use-luis/bot-service-run-console.png)

## <a name="test-the-bot"></a>Testen des Bots

Klicken Sie im Azure-Portal auf **In Webchat testen**, um den Bot zu testen. Geben Sie Nachrichten wie „Eine Notiz erstellen“, „Meine Notizen lesen“ und „Notizen löschen“ ein.
   ![Testen des Notizenbots im Webchat](../media/bot-builder-dotnet-use-luis/bot-service-test-notebot.png)

> [!TIP]
> Wenn Sie feststellen, dass Ihr Bot nicht immer die richtige Absicht bzw. die richtigen Entitäten erkennt, verbessern Sie die Leistung Ihrer LUIS-App, indem Sie sie mit weiteren Beispieläußerungen trainieren. Sie können Ihre LUIS-App erneut trainieren, ohne Änderungen am Code Ihres Bots vorzunehmen. Weitere Informationen finden Sie unter [Hinzufügen von Beispieläußerungen](/azure/cognitive-services/LUIS/add-example-utterances) und [Trainieren und Testen der LUIS-App](/azure/cognitive-services/LUIS/train-test).

> [!TIP]
> Wenn bei Ihrem Botcode ein Problem auftritt, überprüfen Sie Folgendes:
> * Sie haben [den Bot erstellt](./bot-builder-dotnet-luis-dialogs.md#build-the-bot).
> * Ihr Botcode definiert einen Handler für jede Absicht in Ihrer LUIS-App.

## <a name="next-steps"></a>Nächste Schritte

Durch Testen des Bots können Sie sehen, wie Aufgaben von einer LUIS-Absicht aufgerufen werden. Dieses einfache Beispiel lässt jedoch keine Unterbrechung des aktuell aktiven Dialogs zu. Das Zulassen und Verarbeiten von Unterbrechungen wie „Hilfe“ oder „Abbrechen“ stellt ein flexibles Konzept dar, das die tatsächlichen Aktivitäten der Benutzer berücksichtigt. Erfahren Sie mehr über das Verwenden von bewertbaren Dialogen, damit Ihre Dialoge Unterbrechungen verarbeiten können.

> [!div class="nextstepaction"]
> [Globale Meldungshandler, die bewertbare Dialoge verwenden](bot-builder-dotnet-scorable-dialogs.md)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Dialoge](bot-builder-dotnet-dialogs.md)
- [Verwalten des Konversationsflusses mit Dialogen](bot-builder-dotnet-manage-conversation-flow.md)
- <a href="https://www.luis.ai" target="_blank">LUIS</a>
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referenz zum Bot Builder SDK für .NET</a>

[LUIS]: https://www.luis.ai/
[NotesSample]: https://github.com/Microsoft/BotFramework-Samples/tree/master/docs-samples/CSharp/Simple-LUIS-Notes-Sample
[NotesSampleJSON]: https://github.com/Microsoft/BotFramework-Samples/blob/master/docs-samples/CSharp/Simple-LUIS-Notes-Sample/Notes.json

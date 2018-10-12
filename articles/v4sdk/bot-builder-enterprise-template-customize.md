---
title: Anpassen des Bots für Unternehmen | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie den durch die Unternehmensvorlage erstellten Bot anpassen.
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 09/18/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 6fc7f73d406c1bbbc2b2671c9336df6bda10ade6
ms.sourcegitcommit: 87b5b0ca9b0d5e028ece9f7cc4948c5507062c2b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47029758"
---
# <a name="enterprise-bot-template---customize-your-bot"></a>Vorlage für den Bot für Unternehmen: Anpassen des Bots

> [!NOTE]
> Dieses Thema gilt für die SDK-Version v4. 

## <a name="net"></a>.NET
Nachdem Sie den Bot für Unternehmen bereitgestellt und sich vergewissert haben, dass alles ordnungsgemäß funktioniert (wie [hier](bot-builder-enterprise-template-deployment.md) beschrieben), können Sie Ihren Bot ganz einfach an Ihr Szenario und Ihre Anforderungen anpassen. Die Vorlage dient als stabiles Fundament für die Erstellung Ihrer Konversationsumgebung.

## <a name="project-structure"></a>Projektstruktur

Im Anschluss sehen Sie die Ordnerstruktur Ihres Bots. Diese wird für die Strukturierung Ihres Botprojekts sowie für die Verarbeitung eingehender Nachrichten empfohlen.

    | - YourBot.bot         // The .bot file containing all of your Bot configuration including dependencies
    | - README.md           // README file containing links to documentation
    | - Program.cs          // Default Program.cs file
    | - Startup.cs          // Core Bot Initialisation including Bot Configuration LUIS, Dispatcher, etc. 
    | - <BOTNAME>State.cs   // The Root State class for your Bot
    | - appsettings.json    // References above .bot file for Configuration information. App Insights key
    | - CognitiveModels     
        | - LUIS            // .LU file containing base conversational intents (Greeting, Help, Cancel)
        | - QnA             // .LU file containing example QnA items
    | - DeploymentScripts   // msbot clone recipe for deployment
    | - Dialogs             // All Bot dialogs sit under this folder
        | - Main            // Root Dialog for all messages
            | - MainDialog.cs       // Dialog Logic
            | - MainResponses.cs    // Dialog responses
            | - Resources           // Adaptive Card JSON, Resource File
        | - Onboarding
            | - OnboardingDialog.cs       // Onboarding dialog Logic
            | - OnboardingResponses.cs    // Onboarding dialog responses
            | - OnboardingState.cs        // Localised dialog state
            | - Resources                 Resource File
        | - Cancel
        | - Escalate
        | - Signin
    | - Middleware          // Telemetry, Content Moderator
    | - ServiceClients      // SDK libraries, example GraphClient provided for Auth example
   
## <a name="update-introduction-message"></a>Aktualisieren der Einführungsnachricht

Für die Einführungsnachricht wird eine [adaptive Karte](https://www.adaptivecards.io) verwendet. Suchen Sie zum Anpassen dieser Boteinführung im Ordner „Dialogs/Main/Resources“ nach der JSON-Datei ```Intro.json```. Verwenden Sie die [Schnellansicht für die adaptive Karte](http://adaptivecards.io/visualizer), um sie an die Anforderungen Ihres Bots anzupassen.

## <a name="update-bot-responses"></a>Aktualisieren der Botantworten

Jeder Dialog innerhalb des Projekts verfügt über einen Satz von Antworten, die in unterstützenden Ressourcendateien (RESX-Dateien) gespeichert sind. Diese befinden sich jeweils im Unterordner „Resources“ für den jeweiligen Dialog.

Die Antworten können wie im Anschluss gezeigt im Visual Studio-Ressourcen-Editor geändert werden, um die Antwort Ihres Bots anzupassen.

![Anpassen von Botantworten](media/enterprise-template/EnterpriseBot-CustomisingResponses.png)

Mehrsprachige Antworten werden mithilfe des standardmäßigen Lokalisierungsansatzes für Ressourcendateien unterstützt. Weitere Informationen finden Sie [hier](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/localization?view=aspnetcore-2.1).

## <a name="updating-your-cognitive-models"></a>Aktualisieren Ihrer kognitiven Modelle

Die Unternehmensvorlage enthält standardmäßig zwei kognitive Modelle: eine exemplarische QnAMaker-Wissensdatenbank für häufig gestellte Fragen sowie ein LUIS-Modell für allgemeine Absichten (Begrüßung, Hilfe, Abbruch usw.). Diese Modelle können Sie an Ihre Anforderungen anpassen. Darüber hinaus können Sie neue LUIS-Modelle und QnAMaker-Wissensdatenbanken hinzufügen, um die Fähigkeiten Ihres Bots zu erweitern.

### <a name="updating-an-existing-luis-model"></a>Aktualisieren eines vorhandenen LUIS-Modells
Gehen Sie wie folgt vor, um ein vorhandenes LUIS-Modell für die Unternehmensvorlage zu aktualisieren:
1. Nehmen Sie die gewünschten Änderungen am LUIS-Modell über das [LUIS-Portal](http://luis.ai) oder mithilfe der CLI-Tools [LuDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) und [Luis](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) vor. 
2. Führen Sie den folgenden Befehl aus, um Ihr Dispatchmodell mit Ihren Änderungen zu aktualisieren und die ordnungsgemäße Weiterleitung von Nachrichten zu gewährleisten:
```shell
    dispatch refresh --bot "YOURBOT.bot" --secret YOURSECRET
```
3. Führen Sie am Projektstamm für jedes aktualisierte Modell den folgenden Befehl aus, um die zugeordneten LuisGen-Klassen zu aktualisieren: 
```shell
    luis export version --appId [LUIS_APP_ID] --versionId [LUIS_APP_VERSION] --authoringKey [YOUR_LUIS_AUTHORING_KEY] | luisgen - -cs [CS_FILE_NAME] -o "\Dialogs\Shared\Resources"
```

### <a name="updating-an-existing-qnamaker-knowledge-base"></a>Aktualisieren einer vorhandenen QnAMaker-Wissensdatenbank
Führen Sie die folgenden Schritte aus, um eine vorhandene QnAMaker-Wissensdatenbank zu aktualisieren:
1. Nehmen Sie die gewünschten Änderungen an Ihrer QnAMaker-Wissensdatenbank über die CLI-Tools [LuDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) und [QnAMaker](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/QnAMaker) oder über das [QnAMaker-Portal](https://qnamaker.ai) vor.
2. Führen Sie den folgenden Befehl aus, um Ihr Dispatchmodell mit Ihren Änderungen zu aktualisieren und die ordnungsgemäße Weiterleitung von Nachrichten zu gewährleisten:
```shell
    dispatch refresh --bot "YOURBOT.bot" --secret YOURSECRET
```

### <a name="adding-a-new-luis-model"></a>Hinzufügen eines neuen LUIS-Modells

Wenn Sie Ihrem Projekt ein neues LUIS-Modell hinzufügen möchten, müssen Sie Botkonfiguration und Dispatcher aktualisieren, damit das zusätzliche Modell berücksichtigt wird. 
1. Erstellen Sie Ihr LUIS-Modell mithilfe der CLI-Tools „LuDown“ und „LUIS“ oder über das LUIS-Portal.
2. Führen Sie den folgenden Befehl aus, um eine Verbindung zwischen Ihrer neuen LUIS-App und Ihrer BOT-Datei herzustellen:
```shell
    msbot connect luis --appId [LUIS_APP_ID] --authoringKey [LUIS_AUTHORING_KEY] --subscriptionKey [LUIS_SUBSCRIPTION_KEY] 
```
3. Verwenden Sie den folgenden Befehl, um Ihrer Dispatcher-Instanz das neue LUIS-Modell hinzuzufügen:
```shell
    dispatch add -t luis -id YOUR_LUIS_APPID -bot "YOURBOT.bot" -secret YOURSECRET
```
4. Verwenden Sie den folgenden Befehl, um das Dispatchmodell mit den LUIS-Modelländerungen zu aktualisieren:
```shell
    dispatch refresh -bot "YOURBOT.bot" -secret YOURSECRET
```

## <a name="adding-a-new-dialog"></a>Hinzufügen eines neuen Dialogs 

Wenn Sie Ihrem Bot einen neuen Dialog hinzufügen möchten, müssen Sie zunächst unter „Dialogs“ einen neuen Ordner erstellen. Achten Sie dabei darauf, dass sich die Klasse von `EnterpriseDialog` ableitet. Anschließend muss die Dialoginfrastruktur eingerichtet werden. Der Dialog „Onboarding“ zeigt ein einfaches Beispiel, das Sie als Referenz verwenden können. Im Anschluss finden Sie einen Auszug aus diesem Dialog sowie eine Übersicht über die Schritte.

- Hinzufügen eines Wasserfalldialogs zu Ihrem Konstruktor
- Definieren der Schritte für Ihren Wasserfall
- Erstellen Ihrer Wasserfallschritte
- Aufrufen von „AddDialog“ und Übergeben Ihres Wasserfalls
- Aufrufen von „AddDialog“ und Übergeben aller Eingabeaufforderungen, die Sie ggf. in Ihrem Wasserfall verwenden
- Festlegen von „InitialDialogId“ auf den ersten Dialog, der von der Komponente verwendet werden soll

```
InitialDialogId = nameof(OnboardingDialog);

var onboarding = new WaterfallStep[]
{
    AskForName,
    AskForEmail,
    AskForLocation,
    FinishOnboardingDialog,
};

AddDialog(new WaterfallDialog(InitialDialogId, onboarding));
AddDialog(new TextPrompt(NamePrompt));
AddDialog(new TextPrompt(EmailPrompt));
AddDialog(new TextPrompt(LocationPrompt));
```

Als Nächstes müssen Sie den Vorlagen-Manager für die Verarbeitung von Antworten erstellen. Erstellen Sie eine neue, von „TemplateManager“ abgeleitete Klasse. Ein Beispiel finden Sie in der Datei „OnboardingResponses.cs“. Auszug:

```
public const string _namePrompt = "namePrompt";
public const string _haveName = "haveName";
public const string _emailPrompt = "emailPrompt";
      
private static LanguageTemplateDictionary _responseTemplates = new LanguageTemplateDictionary
{
    ["default"] = new TemplateIdMap
    {
        {
            _namePrompt,
            (context, data) => OnboardingStrings.NAME_PROMPT
        },
        {
            _haveName,
            (context, data) => string.Format(OnboardingStrings.HAVE_NAME, data.name)
        },
        {
            _emailPrompt,
            (context, data) => OnboardingStrings.EMAIL_PROMPT
        },
```

Zum Rendern von Antworten können Sie eine Instanz des Vorlagen-Managers verwenden, um auf diese Antworten über `ReplyWith` oder `RenderTemplate` (für Eingabeaufforderungen) zuzugreifen. Im Anschluss finden Sie einige Beispiele.

```
Prompt = await _responder.RenderTemplate(sc.Context, "en", OnboardingResponses._namePrompt),
await _responder.ReplyWith(sc.Context, OnboardingResponses._haveName, new { name });
```

Als Letztes muss für die Dialoginfrastruktur eine auf Ihren Dialog begrenzte State-Klasse erstellt werden. Erstellen Sie eine neue Klasse, und achten Sie darauf, dass sie sich von `DialogState` ableitet.

Der fertig gestellte Dialog muss der Komponente `MainDialog` mithilfe von `AddDialog` hinzugefügt werden. Rufen Sie zur Verwendung Ihres neuen Dialogs `dc.BeginDialogAsync()` innerhalb der Methode `RouteAsync` auf, und verwenden Sie bei Bedarf die entsprechende LUIS-Absicht als Auslöser.

## <a name="conversational-insights-using-powerbi-dashboard-and-application-insights"></a>Konversationsbezogene Erkenntnisse mit Power BI-Dashboard und Application Insights
- Unter [Vorlage für den Bot für Unternehmen: Konversationsanalyse mit Power BI-Dashboard und Application Insights](bot-builder-enterprise-template-powerbi.md) finden Sie eine Einführung in die Gewinnung konversationsbezogener Erkenntnisse.


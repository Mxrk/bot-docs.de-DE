---
title: Botverwaltung mit Bot Builder-Tools
description: Bot Builder-Tools ermöglichen das Verwalten von Botressourcen direkt über die Befehlszeile.
keywords: Bot Builder-Vorlagen, LUDown, QnA, LUIS, MSBot
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: tools
ms.date: 09/18/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: ef57cdf6a202679f9fc3a83e3e44640b43adb67f
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49998359"
---
# <a name="bot-builder-tools"></a>Bot Builder-Tools

Mit Bot Builder-[Tools][cliTools] wird der End-to-End-Workflow für die Bot-Entwicklung abgedeckt, der die Phasen Planen, Entwickeln, Testen, Veröffentlichen, Verbinden und Evaluieren umfasst. Wir sehen uns nun an, wie diese Tools Sie in den einzelnen Phasen des Entwicklungszyklus unterstützen können.

[Planen](#plan)
- Sehen Sie sich zunächst die [Entwurfsrichtlinien](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-design-principles) für Bots an, um sich mit den bewährten Methoden vertraut zu machen.
- Erstellen Sie simulierte Konversationen, indem Sie das [Chatdown](#create-mock-conversations-using-chatdown)-Tool verwenden.

[Build](#build)
- Führen Sie einen Bootstrap-Vorgang für Language Understanding mit [LUDown](#bootstrap-language-understanding-with-ludown) durch.
- Verfolgen Sie Dienstverweise mit [MSBot](#keep-track-of-service-references-using-bot-file).
- Erstellen und verwalten Sie LUIS-Anwendungen mit der [LUIS CLI](#create-and-manage-luis-applications-using-luis-cli).
- Erstellen Sie eine QnA Maker-Wissensdatenbank, indem Sie die [QnA Maker CLI](#create-qna-maker-kb-using-qna-maker-cli) verwenden.
- Erstellen Sie ein Dispatchmodell mit der [Dispatch CLI](#create-dipsatch-model-using-dispatch-cli).

[Test](#test)
- Testen Sie Ihren Bot per [Bot Framework Emulator V4](https://aka.ms/bot-framework-emulator-v4-overview).

[Veröffentlichen](#publish)
- Sie können Bots erstellen, herunterladen und für Azure Bot Service veröffentlichen, indem Sie die [Azure CLI][azureCli] verwenden.

[Herstellen einer Verbindung](#configure-channels)
- Stellen Sie für Ihren Bot eine Verbindung mit Azure Bot Service-Kanälen her, indem Sie die [Azure CLI][azureCli] verwenden.

## <a name="plan"></a>Plan

### <a name="create-mock-conversations-using-chatdown"></a>Erstellen von simulierten Konversationen mit Chatdown

Chatdown ist ein Transkript-Generator, für den eine CHAT-Datei zum Generieren von simulierten Transkripts verwendet wird. Generierte simulierte Transkriptdateien werden an stdout ausgegeben.

Ein guter Bot beginnt – wie jede erfolgreiche Anwendung oder Website – mit eindeutigen Informationen zu den unterstützten Szenarien. Das Erstellen simulierter Versionen von Konversationen zwischen Bot und Benutzer ist für folgende Zwecke nützlich:

- Schaffen des Rahmens für die vom Bot unterstützten Szenarien
- Ermöglichen einer Überprüfung durch Entscheidungsträger und Bereitstellen von Feedback
- Definieren eines „optimalen Pfads“ (und anderer Pfade) für Konversationsflüsse zwischen einem Benutzer und einer Datei im CHAT-Format für den Bot, um simulierte Konversationen zwischen einem Benutzer und einem Bot zu erstellen. Mit dem Chatdown CLI-Tool werden CHAT-Dateien in Konversationstranskripts (TRANSKRIPT-Dateien) konvertiert, die in [Bot Framework Emulator V4](https://github.com/microsoft/botframework-emulator) angezeigt werden können.

Hier ist ein Beispiel für eine `.chat`-Datei angegeben:

```markdown
user=Joe
bot=LulaBot

bot: Hi!
user: yo!
bot: [Typing][Delay=3000]
Greetings!
What would you like to do?
* update - You can update your account
* List - You can list your data
* help - you can get help

user: I need the bot framework logo.

bot:
Here you go.
[Attachment=bot-framework.png]
[Attachment=http://yahoo.com/bot-framework.png]
[AttachmentLayout=carousel]

user: thanks
bot:
Here's a form for you
[Attachment=card.json adaptivecard]

```

### <a name="create-a-transcript-file-from-chat-file"></a>Erstellen einer Transkriptdatei aus einer CHAT-Datei
Ein Chatdown-Befehl sieht wie folgt aus:

```bash
chatdown sample.chat > sample.transcript
```

Hierfür wird `sample.chat` genutzt und `sample.transcript` ausgegeben. Weitere Informationen finden Sie unter [Chatdown CLI][chatdown].

## <a name="build"></a>Entwickeln
### <a name="create-a-luis-application-with-ludown"></a>Erstellen einer LUIS-Anwendung mit LUDown
Das LUDown-Tool kann zum Erstellen von neuen JSON-Modellen für LUIS und QnA verwendet werden.  
Sie können [Absichten](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/add-intents) und [Entitäten](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/add-entities) für eine LUIS-Anwendung auf die gleiche Weise definieren, wie Sie über das LUIS-Portal vorgehen würden.

„#\<intent-name\>“ beschreibt einen neuen Absichtsdefinitionsabschnitt. In den folgenden Zeilen werden [Äußerungen](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/add-example-utterances) aufgelistet, die diese Absicht beschreiben.

Beispielsweise können Sie mehrere LUIS-Absichten in einer einzelnen LU-Datei wie folgt erstellen: 

```LUDown
# Greeting
- Hi
- Hello
- Good morning
- Good evening

# Help
- help
- I need help
- please help
```

### <a name="create-qna-pairs-with-ludown"></a>Erstellen von QnA-Paaren mit LUDown

Das LU-Dateiformat unterstützt auch QnA-Paare mithilfe der folgenden Notation: 

```LUDown
> comment
### ? question ?
  ```markdown
    answer
  ```

Das LUDown-Tool trennt automatisch Fragen und Antworten in einer qnamaker-JSON-Datei, die Sie dann verwenden können, um Ihre neue [QnaMaker.ai](http://qnamaker.ai)-Wissensdatenbank zu erstellen.

```LUDown
### ? How do I change the default message for QnA Maker?
  ```markdown
  You can change the default message if you use the QnAMakerDialog. 
  See [this link](https://docs.botframework.com/en-us/azure-bot-service/templates/qnamaker/#navtitle) for details. 
  ```

Sie können auch mehrere Fragen für dieselbe Antwort hinzufügen, indem Sie einfach neue Zeilen von Variationen der Fragen für eine einzelne Antwort hinzufügen.

```LUDown
### ? What is your name?
- What should I call you?
  ```markdown
    I'm the echoBot! Nice to meet you.
  ```

### <a name="generate-json-models-with-ludown"></a>Generieren von JSON-Modellen mit LUDown

Nachdem Sie LUIS- oder QnA-Sprachkomponenten im LU-Format definiert haben, können Sie diese in einer LUIS-JSON-, QnA-JSON- oder QnA-TSV-Datei veröffentlichen. Bei der Ausführung sucht das LUDown-Tool innerhalb des gleichen Arbeitsverzeichnisses nach LU-Dateien, die es analysieren kann. Da das Tool LUDown LU-Dateien von LUIS oder QnA als Ziel verwenden kann, müssen Sie mithilfe des allgemeinen Befehls **ludown parse <Service> -- in <luFile>** einfach nur den Sprachdienst angeben, für den die Generierung erfolgen soll. 

In unserem Beispielarbeitsverzeichnis können zwei LU-Dateien analysiert werden: „1.lu“ zum Erstellen des LUIS-Modells und „qna1.lu“ zum Erstellen einer QnA-Wissensdatenbank.

#### <a name="generate-luis-json-models"></a>Generieren von LUIS-JSON-Modellen

Um mit LUDown ein LUIS-Modell zu generieren, geben Sie in Ihrem aktuellen Arbeitsverzeichnis einfach Folgendes ein:

```shell
ludown parse ToLuis --in <luFile>
```

#### <a name="generate-qna-knowledge-base"></a>Generieren einer QnA-Wissensdatenbank

Um eine QnA-Wissensdatenbank zu erstellen, müssen Sie analog dazu nur das Analyseziel ändern.

```shell
ludown parse ToQna --in <luFile> 
```

Die sich ergebenden JSON-Dateien können von LUIS und QnA über die jeweiligen Portale oder über die neuen CLI-Tools genutzt werden.

Weitere Informationen finden Sie im GitHub-Repository zur [LUDown CLI][ludown].
## <a name="track-service-references-using-bot-file"></a>Nachverfolgen von Dienstverweisen per BOT-Datei

Das neue [MSBot][msbotCli]-Tool ermöglicht das Erstellen einer **BOT**-Datei, in der Metadaten verschiedener Dienste, die Ihr Bot nutzt, an einem Ort gespeichert werden. Diese Datei ermöglicht Ihrem Bot auch das Herstellern einer Verbindung mit diesen Diensten über die CLI. Das Tool steht als npm-Modul zur Verfügung. Führen Sie zum Installieren Folgendes aus:

```shell
npm install -g msbot
```

Um eine BOT-Datei zu erstellen, geben Sie in der CLI **msbot init** (gefolgt vom Namen Ihres Bots) und den URL-Zielendpunkt ein, beispielsweise:

```shell
msbot init --name TestBot --endpoint http://localhost:9499/api/messages
```
Um Ihren Bot mit einem Dienst zu verbinden, geben Sie in der CLI **msbot connect** ein, gefolgt vom entsprechenden Dienst:

```shell
msbot connect [Service]
```

Eine Liste mit den unterstützten Diensten finden Sie in der [Infodatei][msbotCli].

## <a name="create-and-manage-luis-applications-using-luis-cli"></a>Erstellen und Verwalten von LUIS-Anwendungen mit der LUIS CLI

Im neuen Toolset ist eine [LUIS-Erweiterung][luisCli] enthalten, sodass Sie Ihre LUIS-Ressourcen unabhängig voneinander verwalten können. Sie steht als npm-Modul bereit, das Sie herunterladen können:

```shell
npm install -g luis-apis
```
Die grundlegende Befehlssyntax für das LUIS-Tool aus der CLI lautet folgendermaßen:

```shell
luis <action> <resource> <args...>
```
Um Ihren Bot mit LUIS zu verbinden, müssen Sie eine **LUISRC**-Datei erstellen. Dies ist eine Konfigurationsdatei, die Ihre LUIS-appID und das Kennwort für den Dienstendpunkt bereitstellt, wenn Ihre Anwendung ausgehende Aufrufe ausführt. Sie können diese Datei erstellen, indem Sie **luis init** wie folgt ausführen:

```shell
luis init
```
Im Terminal werden Sie zur Eingabe Ihres LUIS-Dokumenterstellungsschlüssels, der Region und der appID aufgefordert, bevor das Tool die Datei generiert.  

Nachdem diese Datei generiert wurde, ist Ihre Anwendung in der Lage, die LUIS-JSON-Datei (aus LUDown generiert) mithilfe des folgenden Befehls über die Befehlszeilenschnittstelle zu verarbeiten.

```shell
luis import application --in luis-app.json | msbot connect luis --stdin
```
Weitere Informationen finden Sie im GitHub-Repository zur [LUIS CLI][luisCli].

## <a name="create-qna-maker-kb-using-qna-maker-cli"></a>Erstellen einer QnA Maker-Wissensdatenbank mit der QnA Maker CLI

Im neuen Toolset ist eine [QnA-Erweiterung][qnaCli] enthalten, sodass Sie Ihre LUIS-Ressourcen unabhängig voneinander verwalten können. Sie steht als npm-Modul bereit, das Sie herunterladen können.

```shell
npm install -g qnamaker
```
Mit dem QnA Maker-Tool können Sie Ihre Wissensdatenbank erstellen, aktualisieren, veröffentlichen, löschen und trainieren. Sie können Dateien verwenden, die mit dem Befehl [ludown parse toqna](#generate-qna-knowledge-base) generiert wurden, um eine Wissensdatenbank zu erstellen oder zu ersetzen.

```shell
qnamaker create --in qnaKB.json --msbot | msbot connect qna --stdin
```

Weitere Informationen finden Sie im GitHub-Repository zur [QnA Maker CLI][qnaCli].

## <a name="create-dispatch-model-using-dispatch-cli"></a>Erstellen eines Dispatchmodells mit der Dispatch CLI

Dispatch ist ein Tool zum Erstellen und Evaluieren von LUIS-Modellen, die zum Verteilen von Absichten auf mehrere Botmodule verwendet werden, z.B. LUIS-Modelle, QnA-Wissensdatenbanken und andere (werden Dispatch als Dateityp hinzugefügt).

Verwenden Sie das Dispatchmodell in den folgenden Fällen:

- Ihr Bot besteht aus mehreren Modulen, und Sie benötigen Hilfe beim Weiterleiten der Äußerungen von Benutzern an diese Module und beim Evaluieren der Botintegration.
- Sie evaluieren die Qualität der Absichtsklassifizierung eines einzelnen LUIS-Modells.
- Sie erstellen ein Textklassifizierungsmodell aus Textdateien.

Nachdem Sie Ihre BOT-Datei mit den [LUIS-Anwendungen][msbotCli-luis] und [QnA Maker-Wissensdatenbanken][msbotCli-qna] für Ihren Bot zusammengestellt haben, können Sie ein Dispatchmodell einfach wie folgt erstellen: 

```shell
dispatch create -b <YOUR-BOT-FILE> | msbot connect dispatch --stdin
```
Weitere Informationen finden Sie unter [Dispatch CLI][dispatchCli].

## <a name="test"></a>Test

[Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases) ist eine Desktopanwendung, mit der Botentwickler ihre Bots auf localhost oder per Remoteausführung über einen Tunnel testen und debuggen können.

## <a name="publish"></a>Veröffentlichen

Sie können die [Azure CLI][azureCli] nutzen, um Ihren Bot für Azure Bot Service zu [erstellen](#create-azure-bot-service-bot), [herunterzuladen](#download-azure-bot-service-bot) und zu [veröffentlichen](#publish-azure-bot-service-bot). Installieren Sie die Boterweiterung wie folgt: 
```shell
az extension add -n botservice
```

## <a name="create-azure-bot-service-bot"></a>Erstellen eines Azure Bot Service-Bots

Melden Sie sich bei Ihrem Azure-Konto an. 
```shell
az login
```

Nach dem Anmelden können Sie wie folgt einen neuen Azure Bot Service-Bot erstellen: 
```shell
az bot create [options]
```

Gehen Sie wie folgt vor, um einen Bot zu erstellen und die BOT-Datei mit der Botkonfiguration zu aktualisieren:  
```shell
az bot create [options] --msbot | msbot connect bot --stdin
```

Bei einem bereits vorhandenen Bot:  
```shell
az bot show [options] --msbot | msbot connect bot --stdin
```

| Option                            | BESCHREIBUNG                                   |
|-----------------------------------|-----------------------------------------------|
| --kind -k [Erforderlich]              | Die Art des Bots.  Zulässige Werte: function, registration, webapp.|
| --name -n [Erforderlich]              | Der Ressourcenname des Bots. |
| --appid                           | Die ID des MSA-Kontos, das für den Bot verwendet wird.   |
| --location -l                     | Der Standort. Sie können den standardmäßig verwendeten Standort mit `az configure --defaults location=<location>` konfigurieren.  Standard: westus.|
| --msbot                           | Die Ausgabe wird im JSON-kompatiblen Format mit einer BOT-Datei angezeigt.  Zulässige Werte: false, true.|
| --password -p                     | Das MSA-Kennwort für den Bot aus dem Entwicklerportal. |
| --resource-group -g               | Name der Ressourcengruppe Sie können die Standardgruppe mit `az configure --defaults group=<name>` konfigurieren.  Standard: build2018. |
| --tags                            | Satz mit Tags, die dem Bot hinzugefügt werden sollen. |


### <a name="configure-channels"></a>Konfigurieren von Kanälen

Sie können die Azure CLI nutzen, um Kanäle für Ihren Bot zu verwalten. 
```shell
>az bot -h
Group
   az bot: Manage Bot Services.
    Subgroups:
        directline: Manage Directline Channel on a Bot.
        email     : Manage Email Channel on a Bot.
        facebook  : Manage Facebook Channel on a Bot.
        kik       : Manage Kik Channel on a Bot.
        msteams   : Manage Msteams Channel on a Bot.
        skype     : Manage Skype Channel on a Bot.
        slack     : Manage Slack Channel on a Bot.
        sms       : Manage Sms Channel on a Bot.
        telegram  : Manage Telegram Channel on a Bot.
        webchat   : Manage Webchat Channel on a Bot.

    Commands:
        create    : Create a new Bot Service.
        delete    : Delete an existing Bot Service.
        download  : Download an existing Bot Service.
        publish   : Publish to an existing Bot Service.
        show      : Get an existing Bot Service.
        update    : Update an existing Bot Service.

```

## <a name="additional-information"></a>Zusätzliche Informationen
- [Bot Builder-Tools][cliTools]

<!-- Footnote links -->

[cliTools]: https://aka.ms/botbuilder-tools-readme
[azureCli]: https://aka.ms/botbuilder-tools-azureCli
[msbotCli]: https://aka.ms/botbuilder-tools-msbot-readme
[msbotCli-luis]: https://aka.ms/botbuilder-tools-msbot-readme#connecting-to-luis-application
[msbotCli-qna]: https://aka.ms/botbuilder-tools-msbot-readme#connecting-to-qna-maker-knowledge-base
[chatdown]: https://aka.ms/botbuilder-tools-chatdown
[ludown]: https://aka.ms/botbuilder-ludown
[luisCli]: https://aka.ms/botbuilder-luis-cli
[qnaCli]: https://aka.ms/botbuilder-tools-qnaMaker
[dispatchCli]: https://aka.ms/botbuilder-tools-dispatch

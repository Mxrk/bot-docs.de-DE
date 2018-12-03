---
title: Erstellen von Bots mit der Azure CLI
description: Botgeneratortools ermöglichen das Verwalten von Botressourcen direkt über die Befehlszeile.
author: matthewshim-ms
ms.author: v-shimma
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: tools
ms.date: 10/31/2018
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 8a59c0a8b7ee664cdb38ab9d0cb186114938d73f
ms.sourcegitcommit: 782b3a2e788c25effd7d150a070bd2819ea92dad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50743664"
---
# <a name="create-bots-with-azure-cli"></a>Erstellen von Bots mit der Azure CLI

[!INCLUDE [pre-release-label](./includes/pre-release-label-v3.md)]

In diesem Tutorial lernen Sie Folgendes: 
- Erstellen eines neuen Bots mithilfe der Azure CLI 
- Herunterladen einer lokalen Kopie für die Entwicklung
- Verwenden des neuen MSBot-Tools zum Speichern sämtlicher Botressourceninformationen
- Verwalten, Erstellen oder Aktualisieren von LUIS- und QnA-Modellen mit LUDown
- Herstellen einer Verbindung mit LUIS und QnA Maker Services über die CLI
- Bereitstellen Ihres Bots in Azure über die CLI

## <a name="prerequisites"></a>Voraussetzungen

Um diese Tools über die Befehlszeile nutzen zu können, muss Node.js auf Ihrem Computer installiert sein: 
- [Node.js (v8. 5 oder höher)](https://nodejs.org/en/)

## <a name="1-install-tools"></a>1. Installieren von Tools
1. [Installieren](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) Sie die aktuelle Version der Azure-Befehlszeilenschnittstelle (CLI).
2. [Installieren](https://aka.ms/botbuilder-tools-readme) Sie die Bot Builder-Tools.

Sie können jetzt Bots mit der Azure CLI wie jede andere Azure-Ressource verwalten.

>[!TIP]
> Die Azure-Boterweiterung unterstützt zurzeit nur v3-Bots.
  
3. Melden Sie sich an der Azure CLI an, indem Sie den folgenden Befehl ausführen.

```azurecli
az login
```
Ein Browserfenster wird geöffnet, in dem Sie sich anmelden können. Nach dem Anmelden wird die folgende Meldung angezeigt:

![MS-Geräteanmeldung](media/bot-builder-tools/az-browser-login.png)

Im Befehlszeilenfenster werden die folgenden Informationen angezeigt:

![Befehl für Azure-Anmeldung](media/bot-builder-tools/az-login-command.png)

## <a name="2-create-a-new-bot-from-azure-cli"></a>2. Erstellen eines neuen Bots über die Azure CLI

Verwenden Sie die Azure CLI, um neue Bots ausschließlich über die Befehlszeile zu erstellen. 

```azurecli
az bot [command]
```
|Befehle|  |
|----|----|
| create      |Hinzufügen einer Ressource|
| delete     |Klonen einer Ressource|
| Download   | Herunterladen des Botquellcodes|
| Veröffentlichen   |Veröffentlichen in einem vorhandenen Botdienst|
| show |Anzeigen vorhandener Botressourcen|
| aktualisieren| Aktualisieren eines vorhandenen Botdiensts|

Um einen neuen Bot mithilfe der CLI zu erstellen, müssen Sie eine vorhandene [Ressourcengruppe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) auswählen oder eine neue Ressourcengruppe erstellen. 

```azurecli
az bot create --resource-group "my-resource-group" --name "my-bot-name" --kind "my-resource-type" --version v3 --description "description-of-my-bot" --lang "programming-language"
```
Zulässige Werte für `--kind` sind `function, registration, webapp`, und für `--version` sind die Werte `v3, v4` zulässig.  Wenn Sie das Argument `--lang` nicht angeben, wird ein .NET-Bot erstellt. Verwenden Sie `Node`, um einen Node-Bot zu erstellen.

Nach einer erfolgreichen Anforderung wird die Bestätigungsmeldung angezeigt.
```
Obtained msa app id and password. Provisioning bot now.
```

> [!TIP]
> Wenn Sie eine Fehlermeldung erhalten, dass die **Ressourcengruppe nicht gefunden wurde**, müssen Sie ggf. Ihr [Abonnement](https://docs.microsoft.com/en-us/azure/architecture/cloud-adoption-guide/adoption-intro/subscription-explainer) in der Azure CLI festlegen. Das Azure-Abonnement muss mit dem Abonnement übereinstimmen, das Sie beim Erstellen der Ressourcengruppe eingegeben haben. Geben Sie zum Festlegen des Abonnements Folgendes ein.
> ```azurecli
> az account set --subscription "your-subscription-name"
> ```
> Geben Sie den folgenden Befehl ein, um eine Liste der Abonnements für Ihr Konto anzuzeigen.
> ```azurecli
> az account list
> ```

Ihr neuer Echobot wird für Ihre Ressourcengruppe in Azure bereitgestellt. Um ihn zu testen, wählen Sie einfach **In Webchat testen** unter der Botverwaltungsüberschrift der Ansicht „Web-App-Bot“ aus. 

![Azure-Echobot](media/bot-builder-tools/az-echo-bot.png) 

## <a name="3-download-the-bot-locally"></a>3. Lokales Herunterladen des Bots

Es gibt zwei Möglichkeiten, den Quellcode herunterzuladen:
- Über das Azure-Portal
- Mit der neuen Azure CLI

Um Ihren Botquellcode über das [Azure-Portal](http://portal.azure.com) herunterzuladen, wählen Sie einfach Ihre Botressource und unter „Botverwaltung“ dann die Option **Erstellen** aus. Es gibt mehrere Optionen zum lokalen Verwalten oder Abrufen des Quellcodes des Bots.

![Botdownload über das Azure-Portal](media/bot-builder-tools/az-portal-manage-code.png)

Geben Sie zum Herunterladen des Botquellcodes mit der CLI den folgenden Befehl ein. Ihr Bot wird an einen lokalen Speicherort heruntergeladen.

```azurecli
az bot download --name "my-bot-name" --resource-group "my-resource-group"
```

![CLI-Downloadbefehl](media/bot-builder-tools/cli-bot-download-command.png)

## <a name="4-store-your-bot-information-with-msbot"></a>4. Speichern der Botinformationen mit MSBot

Das neue MSBot-Tool ermöglicht das Erstellen einer **BOT**-Datei, in der Metadaten verschiedener Dienste, die Ihr Bot nutzt, an einem Ort gespeichert werden. Diese Datei ermöglicht Ihrem Bot auch das Herstellern einer Verbindung mit diesen Diensten über die CLI. MSBot-Tools unterstützen verschiedene Befehle. Ausführlichere Informationen finden Sie in der [Infodatei](https://aka.ms/botbuilder-tools-msbot-readme). 

Führen Sie Folgendes aus, um MSBot zu installieren:

```shell
npm install -g msbot
```

Um eine BOT-Datei zu erstellen, geben Sie in der CLI **msbot init** gefolgt vom Namen Ihres Bots und den URL-Zielendpunkt ein, beispielsweise:

```shell
msbot init --name name-of-my-bot --endpoint http://localhost:bot-port-number/api/messages
```

Um Ihren Bot mit einem Dienst zu verbinden, geben Sie in der CLI **msbot connect** ein, gefolgt vom entsprechenden Dienst:

```shell
msbot connect service-type
```

| Dienstart | BESCHREIBUNG |
| ------ | ----------- |
| azure  |Verbinden Ihres Bots mit einer Azure Bot Service-Registrierung|
|endpoint| Verbinden Ihres Bots mit einem Endpunkt, z.B. „localhost“|
|luis     | Verbinden Ihres Bots mit einer LUIS-Anwendung |
| qna     |Verbinden Ihres Bots mit einer QnA-Wissensdatenbank|
|help [cmd]  |Anzeigen von Hilfe zu [cmd]|

Eine vollständige Liste mit den unterstützten Diensten finden Sie in der [Infodatei](https://aka.ms/botbuilder-tools-msbot-readme).

### <a name="connect-your-bot-to-abs-with-the-bot-file"></a>Verbinden Ihres Bots mit ABS mit der BOT-Datei

Wenn das MSBot-Tool installiert ist, können Sie Ihren Bot ganz einfach mit einer vorhandenen Ressourcengruppe in Azure Bot Service verbinden, indem Sie den az bot-Befehl **show** ausführen.

```azurecli
az bot show -n my-bot-name -g my-resource-group --msbot | msbot connect azure --stdin
```

Auf diese Weise werden der aktuelle Endpunkt, die MSA-appID und das Kennwort aus der Zielressourcengruppe übernommen und die Informationen in der BOT-Datei entsprechend aktualisiert.


## <a name="5-manage-update-or-create-luis-and-qna-services-with--new-botbuilder-tools"></a>5. Verwalten, Aktualisieren oder Erstellen von LUIS- und QnA-Diensten mit den neuen Botgeneratortools

Die [Botgeneratortools](https://aka.ms/botbuilder-tools) sind ein neues Toolset, das Sie zum Verwalten und Interagieren mit Ihren Botressourcen direkt über die Befehlszeile verwenden können.

>[!TIP]
> Jedes Botgeneratortool enthält einen globalen Hilfebefehl, auf den über die Befehlszeile durch die Eingabe von **-h** oder **--help** zugegriffen werden kann. Dieser Befehl steht jederzeit in beliebigen Aktionen zur Verfügung und bietet eine nützliche Anzeige der verfügbaren Optionen sowie deren Beschreibungen.

### <a name="ludown"></a>LUDown

[LUDown](https://aka.ms/botbuilder-ludown) ermöglicht Ihnen das Beschreiben und Erstellen leistungsfähiger Sprachkomponenten für Bots unter Verwendung von **LU**-Dateien. Die neue LU-Datei ist eine Art von Markdownformat, die das LUDown-Tool nutzt. Es gibt dann JSON-Dateien aus, die für den Zieldienst spezifisch sind. Zurzeit können Sie LU-Dateien verwenden, um eine neue [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app)-Anwendung oder eine [QnA](https://qnamaker.ai/Documentation/CreateKb)-Wissensdatenbank mit jeweils unterschiedlichen Formaten zu erstellen. LUDown ist als npm-Modul verfügbar und kann verwendet werden, indem Sie das Tool global auf Ihrem Computer installieren:

```shell
npm install -g ludown
```

Das LUDown-Tool kann zum Erstellen von neuen JSON-Modellen für LUIS und QnA verwendet werden.  

### <a name="creating-a-luis-application-with-ludown"></a>Erstellen einer LUIS-Anwendung mit LUDown

Sie können [Absichten](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) und [Entitäten](https://docs.microsoft.com/azure/cognitive-services/luis/add-entities) für eine LUIS-Anwendung auf die gleiche Weise definieren, wie Sie über das LUIS-Portal vorgehen würden.

`# \<intent-name\>` beschreibt einen neuen Absichtsdefinitionsabschnitt. Nachfolgende Zeilen enthalten [Äußerungen](https://docs.microsoft.com/azure/cognitive-services/luis/add-example-utterances), die diese Absicht beschreiben.

Beispielsweise können Sie mehrere LUIS-Absichten in einer einzelnen LU-Datei wie folgt erstellen: 

```ludown
# Greeting
Hi
Hello
Good morning
Good evening

# Help
help
I need help
please help
```

### <a name="qna-pairs-with-ludown"></a>QnA-Paare mit LUDown

Das LU-Dateiformat unterstützt auch QnA-Paare mithilfe der folgenden Notation: 

  ```ludown
  > This is a comment. QnA definitions have the general format:
  ### ? this-is-the-question-string
  - this-is-an-alternate-form-of-the-same-question
  - this-is-another-one
    ```markdown
    this-is-the-answer
    ```
  ```
Das LUDown-Tool trennt automatisch Fragen und Antworten in einer qnamaker-JSON-Datei, die Sie dann verwenden können, um Ihre neue [QnaMaker.ai](http://qnamaker.ai)-Wissensdatenbank zu erstellen.

  ```ludown
  ### ? How do I change the default message for QnA Maker?
    ```markdown
    You can change the default message if you use the QnAMakerDialog. 
    See [this link](https://docs.botframework.com/en-us/azure-bot-service/templates/qnamaker/#navtitle) for details. 
    ```
  ```

Sie können auch mehrere Fragen für dieselbe Antwort hinzufügen, indem Sie einfach neue Zeilen von Variationen der Fragen für eine einzelne Antwort hinzufügen. 

  ```ludown
  ### ? What is your name?
  - What should I call you?
    ```markdown
    I'm the echoBot! Nice to meet you.
    ```
  ```

### <a name="generating-json-models-with-ludown"></a>Generieren von JSON-Modellen mit LUDown

Nachdem Sie LUIS- oder QnA-Sprachkomponenten im LU-Format definiert haben, können Sie diese in einer LUIS-JSON-, QnA-JSON- oder QnA-TSV-Datei veröffentlichen. Bei der Ausführung sucht das LUDown-Tool innerhalb des gleichen Arbeitsverzeichnisses nach LU-Dateien, die es analysieren kann. Da das LUDown-Tool LU-Dateien von LUIS oder QnA als Ziel verwenden kann, müssen Sie mithilfe des allgemeinen Befehls **ludown parse<Service> --in <luFile>** einfach nur den Sprachdienst angeben, für den die Generierung erfolgen soll. 

In unserem Beispielarbeitsverzeichnis können zwei LU-Dateien analysiert werden: „luis-sample.lu“ zum Erstellen des LUIS-Modells und „qna-sample.lu“ zum Erstellen einer QnA-Wissensdatenbank.


#### <a name="generate-luis-json-models"></a>Generieren von LUIS-JSON-Modellen

**luis-sample.lu** 
```ludown
# Greeting
- Hi
- Hello
- Good morning
- Good evening
```

Um mit LUDown ein LUIS-Modell zu generieren, geben Sie in Ihrem aktuellen Arbeitsverzeichnis einfach Folgendes ein:

```shell
ludown parse ToLuis --in ludown-file-name.lu
```

#### <a name="generate-qna-knowledge-base-json"></a>Generieren einer JSON-Datei für eine QnA-Wissensdatenbank

**qna-sample.lu**
  ```ludown
  > This is a sample ludown file for QnA Maker.

  ### ? How do I change the default message
    ```markdown
    You can change the default message if you use the QnAMakerDialog. 
    See [this link](https://docs.botframework.com/en-us/azure-bot-service/templates/qnamaker/#navtitle) for details. 
    ```
  ```

Um eine QnA-Wissensdatenbank zu erstellen, müssen Sie analog dazu nur das Analyseziel ändern. 

```shell
ludown parse ToQna --in ludown-file-name.lu
```

Die sich ergebenden JSON-Dateien können von LUIS und QnA über die jeweiligen Portale oder über die neuen CLI-Tools genutzt werden. 

## <a name="6-connect-to-luis-an-qna-maker-services-from-the-cli"></a>6. Herstellen einer Verbindung mit LUIS und QnA Maker Services über die CLI

### <a name="connect-to-luis-from-the-cli"></a>Herstellen einer Verbindung mit LUIS über die CLI 

Im neuen Toolset ist eine [LUIS-Erweiterung](https://aka.ms/botbuilder-luis-cli) enthalten, sodass Sie Ihre LUIS-Ressourcen unabhängig voneinander verwalten können. Sie steht als npm-Modul bereit, das Sie herunterladen können:

```shell
npm install -g luis-apis
```
Die grundlegende Befehlssyntax für das LUIS-Tool aus der CLI lautet folgendermaßen:

```shell
luis action-name resource-name arguments-list
```
Um Ihren Bot mit LUIS zu verbinden, müssen Sie eine **LUISRC**-Datei erstellen. Dies ist eine Konfigurationsdatei, die Ihre LUIS-appID und das Kennwort für den Dienstendpunkt bereitstellt, wenn Ihre Anwendung ausgehende Aufrufe ausführt. Sie können diese Datei erstellen, indem Sie **luis init** wie folgt ausführen:

```shell
luis init
```
Im Terminal werden Sie zur Eingabe Ihres LUIS-Dokumenterstellungsschlüssels, der Region und der appID aufgefordert, bevor das Tool die Datei generiert.  

![LUIS init](media/bot-builder-tools/luis-init.png) 


Nach diese Datei generiert wurde, ist Ihre Anwendung in der Lage, die LUIS-JSON-Datei (aus LUDown generiert) mithilfe des folgenden Befehls über die CLI zu nutzen: 

```shell
luis import application --in luis-app.json | msbot connect luis --stdin
```

### <a name="connect-to-qna-from-the-cli"></a>Herstellen einer Verbindung mit QnA über die CLI

Im neuen Toolset ist eine [QnA-Erweiterung](https://aka.ms/botbuilder-tools-qnaMaker) enthalten, sodass Sie Ihre LUIS-Ressourcen unabhängig voneinander verwalten können. Sie steht als npm-Modul bereit, das Sie herunterladen können:

```shell
npm install -g qnamaker
```
Mit dem QnA Maker-Tool können Sie Ihre Wissensdatenbank erstellen, aktualisieren, veröffentlichen, löschen und trainieren. Als ersten Schritt müssen Sie eine **QNAMAKERRC**-Datei erstellen, die erforderlich ist, um den Endpunkt für Ihren Dienst zu aktivieren. Sie können diese Datei ganz einfach erstellen, indem Sie **qnamaker init** ausführen und den Eingabeaufforderungen mit Ihrer QnA Maker-Wissensdatenbank-ID folgen. 

```shell
qnamaker init 
```
![QnaMaker init](media/bot-builder-tools/qnamaker-init.png)

Nachdem Ihre QNAMAKERRC-Datei generiert wurde, können Sie eine Verbindung mit Ihrer QnA-Wissensdatenbank herstellen, um die JSON-/TSV-Datei der Wissensdatenbank (aus LUDown generiert) mithilfe des folgenden Befehls zu nutzen:

```shell
qnamaker create --in qnaKB.json --msbot | msbot connect qna --stdin
```

## <a name="7-publish-to-azure-from-the-cli"></a>7. Veröffentlichen in Azure über die CLI

Nachdem Sie Änderungen am Quellcode des Bots vorgenommen haben, können Sie Ihre Änderungen nahtlos veröffentlichen, indem Sie Folgendes ausführen:

```azurecli
az bot publish --name "my-bot-name" --resource-group "my-resource-group"
```

## <a name="references"></a>Referenzen
- [Bot Builder-Tools](https://aka.ms/botbuilder-tools-readme)
- [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)

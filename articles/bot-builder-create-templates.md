---
title: Erstellen von Bots mit Bot Builder-Vorlagen
description: Bot Builder-Tools ermöglichen das Verwalten von Botressourcen direkt über die Befehlszeile.
keywords: Bot Builder-Vorlagen, Node.js, Python, Java, .NET, Ludown, Yeoman
author: matthewshim-ms
ms.author: v-shimma
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 04/25/2018
ms.openlocfilehash: 60cdc3de200336b00173749a553205a47a32457e
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39298616"
---
# <a name="create-bots-with-botbuilder-templates"></a>Erstellen von Bots mit Bot Builder-Vorlagen

Vorlagen zum Erstellen von Bots sind nun in jeder Bot Builder SDK-Plattform verfügbar: 

- Node.js
- Python
- Java
- .NET

## <a name="prerequisites"></a>Voraussetzungen

Die Vorlagen für Node.js, Java und Python erstellen einen einfachen Echobot und werden über [Yeoman](http://yeoman.io/) zur Verfügung gestellt.

- [Node.js](https://nodejs.org/en/) v8.5 oder höher
- [Yeoman](http://yeoman.io/)

Wenn Sie Yeoman noch nicht installiert haben, ist eine globale Installation erforderlich.

```shell
npm install -g yo
```

## <a name="nodejs-python-java-templates"></a>Node.js-, Python-, Java-Vorlagen
Wechseln Sie an der Befehlszeile über cd in einen neuen Ordner Ihrer Wahl. Es sind zwei Versionen der Bot Builder-Node.js-Vorlagen verfügbar, die für **v3** bzw. **v4** des SDK ausgelegt sind. Python- und Java-Vorlagen sind nur für v4 verfügbar. 

So installieren Sie den **Node.js v3-Bot Builder**-Vorlagengenerator

```shell
npm install generator-botbuilder
```

So installieren Sie den **Node.js v4-Bot Builder**-Vorlagengenerator
```shell
npm install generator-botbuilder@preview
```

Die Python- und Java-Vorlagen sind **nur für v4 verfügbar**. 

**Python:**
```shell
npm install generator-botbuilder-python
```

**Java:**
```shell
npm install generator-botbuilder-java
```

Nach der Installation eines Generators können Sie einfach den Befehl **yo** in Ihrer Befehlszeilenschnittstelle ausführen, um eine Liste der in Yeoman verfügbaren Generatoren anzuzeigen.

![Yeoman-Schnittstelle](media/botbuilder-templates/yeoman-generator-botbuilder.png)

Wechseln Sie zu einem Arbeitsverzeichnis Ihrer Wahl, und wählen Sie den Generator aus, der verwendet werden soll. Sie werden nach verschiedenen Optionen zum Erstellen des Bots gefragt, z.B. Name und Beschreibung. Wenn alle Fragen beantwortet sind, wird die Echobotvorlage im selben Arbeitsverzeichnis erstellt.

![Node.js-Yeoman-Vorlage](media/botbuilder-templates/new-template-node.png)


## <a name="net"></a>.NET

Für .NET sind zwei Vorlagen verfügbar, die für **v3** bzw. **v4** des SDK ausgelegt sind. Beide sind als [VSIX](https://docs.microsoft.com/en-us/visualstudio/extensibility/anatomy-of-a-vsix-package)-Pakete verfügbar. Klicken Sie zum Herunterladen auf einen der folgenden Links zum [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

- [Bot Builder V3-Vorlage](https://marketplace.visualstudio.com/items?itemName=BotBuilder.BotBuilderV3)
- [Bot Builder V4-Vorlage](https://aka.ms/Ylcwxk)

#### <a name="prerequisites"></a>Voraussetzungen

- [Visual Studio 2015 oder höher](https://www.visualstudio.com/downloads/)
- [Azure-Konto](https://azure.microsoft.com/en-us/free/)

### <a name="install-the-templates"></a>Installieren der Vorlagen

Öffnen Sie im gespeicherten Verzeichnis einfach das VSIX-Paket, dann wird die Bot Builder-Vorlage in Visual Studio installiert und beim nächsten Öffnen zur Verfügung gestellt.

![VSIX-Paket](media/botbuilder-templates/botbuilder-vsix-template.png)

Um ein neues Botprojekt mithilfe der Vorlage zu erstellen, öffnen Sie einfach Visual Studio und wählen **Datei** > **Neu** > **Projekt** aus. Wählen Sie in Visual C# **Bot Framework** > „Simple Echo Bot Application“ (Einfache Echobotanwendung) aus. Dadurch wird lokal ein neues Echobotprojekt erstellt, das Sie nach Wunsch bearbeiten können. 

![.NET-Botvorlage](media/botbuilder-templates/new-template-dotnet.png)

## <a name="store-your-bot-information-with-msbot"></a>Speichern der Botinformationen mit MSBot

Das neue [MSBot](https://github.com/Microsoft/botbuilder-tools/tree/master/MSBot)-Tool ermöglicht das Erstellen einer **BOT**-Datei, in der Metadaten zu verschiedenen Diensten, die Ihr Bot nutzt, an einem Ort gespeichert werden. Diese Datei ermöglicht Ihrem Bot auch das Herstellern einer Verbindung mit diesen Diensten über die CLI. Das Tool steht als npm-Modul zur Verfügung. Führen Sie zum Installieren Folgendes aus:

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

| Dienst | BESCHREIBUNG |
| ------ | ----------- |
| azure  |Verbinden Ihres Bots mit einer Azure Bot Service-Registrierung|
|localhost| Verbinden Ihres Bots mit einem localhost-Endpunkt|
|luis     | Verbinden Ihres Bots mit einer LUIS-Anwendung |
| qna     |Verbinden Ihres Bots mit einer QnA-Wissensdatenbank|
|help [cmd]  |Anzeigen von Hilfe zu [cmd]|

### <a name="connect-your-bot-to-abs-with-the-bot-file"></a>Verbinden Ihres Bots mit ABS mit der BOT-Datei

Wenn das MSBot-Tool installiert ist, können Sie Ihren Bot ganz einfach mit einer vorhandenen Ressourcengruppe in Azure Bot Service verbinden, indem Sie den az bot-Befehl **show** ausführen. 

```azurecli
az bot show -n <botname> -g <resourcegroup> --msbot | msbot connect azure --stdin
```

Auf diese Weise werden der aktuelle Endpunkt, die MSA-appID und das Kennwort aus der Zielressourcengruppe übernommen und die Informationen in der BOT-Datei entsprechend aktualisiert. 


## <a name="manage-update-or-create-luis-and-qna-services-with--new-botbuilder-tools"></a>Verwalten, Aktualisieren oder Erstellen von LUIS- und QnA-Diensten mit den neuen Botgeneratortools

Die [Botgeneratortools](https://github.com/microsoft/botbuilder-tools) sind ein neues Toolset, das Sie zum Verwalten und Interagieren mit Ihren Botressourcen direkt über die Befehlszeile verwenden können. 

>[!TIP]
> Jedes Botgeneratortool enthält einen globalen Hilfebefehl, auf den über die Befehlszeile durch die Eingabe von **-h** oder **--help** zugegriffen werden kann. Dieser Befehl steht jederzeit in beliebigen Aktionen zur Verfügung und bietet eine nützliche Anzeige der verfügbaren Optionen sowie deren Beschreibungen. 

### <a name="ludown"></a>LUDown
[LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/Ludown) ermöglicht Ihnen das Beschreiben und Erstellen leistungsfähiger Sprachkomponenten für Bots unter Verwendung von **LU**-Dateien. Die neue LU-Datei ist eine Art von Markdownformat, die das LUDown-Tool nutzt. Es gibt dann JSON-Dateien aus, die für den Zieldienst spezifisch sind. Zurzeit können Sie LU-Dateien verwenden, um eine neue [LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-get-started-create-app)-Anwendung oder eine [QnA](https://qnamaker.ai/Documentation/CreateKb)-Wissensdatenbank mit jeweils unterschiedlichen Formaten zu erstellen. LUDown ist als npm-Modul verfügbar und kann verwendet werden, indem Sie das Tool global auf Ihrem Computer installieren:

```shell
npm install -g ludown
```
Das LUDown-Tool kann zum Erstellen von neuen JSON-Modellen für LUIS und QnA verwendet werden.  


### <a name="creating-a-luis-application-with-ludown"></a>Erstellen einer LUIS-Anwendung mit LUDown

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

### <a name="qna-pairs-with-ludown"></a>QnA-Paare mit LUDown

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

### <a name="generating-json-models-with-ludown"></a>Generieren von JSON-Modellen mit LUDown

Nachdem Sie LUIS- oder QnA-Sprachkomponenten im LU-Format definiert haben, können Sie diese in einer LUIS-JSON-, QnA-JSON- oder QnA-TSV-Datei veröffentlichen. Bei der Ausführung sucht das LUDown-Tool innerhalb des gleichen Arbeitsverzeichnisses nach LU-Dateien, die es analysieren kann. Da das Tool LUDown LU-Dateien von LUIS oder QnA als Ziel verwenden kann, müssen Sie mithilfe des allgemeinen Befehls **ludown parse <Service> -- in <luFile>** einfach nur den Sprachdienst angeben, für den die Generierung erfolgen soll. 

In unserem Beispielarbeitsverzeichnis können zwei LU-Dateien analysiert werden: „1.lu“ zum Erstellen des LUIS-Modells und „qna1.lu“ zum Erstellen einer QnA-Wissensdatenbank.

#### <a name="generate-luis-json-models"></a>Generieren von LUIS-JSON-Modellen

Um mit LUDown ein LUIS-Modell zu generieren, geben Sie in Ihrem aktuellen Arbeitsverzeichnis einfach Folgendes ein:

```shell
ludown parse ToLuis --in <luFile> 
```

#### <a name="generate-qna-knowledge-base-json"></a>Generieren einer JSON-Datei für eine QnA-Wissensdatenbank

Um eine QnA-Wissensdatenbank zu erstellen, müssen Sie analog dazu nur das Analyseziel ändern. 

```shell
ludown parse ToQna --in <luFile> 
```

Die sich ergebenden JSON-Dateien können von LUIS und QnA über die jeweiligen Portale oder über die neuen CLI-Tools genutzt werden. 

## <a name="connect-to-luis-an-qna-maker-services-from-the-cli"></a>Herstellen einer Verbindung mit LUIS und QnA Maker Services über die CLI

### <a name="connect-to-luis-from-the-cli"></a>Herstellen einer Verbindung mit LUIS über die CLI 

Im neuen Toolset ist eine [LUIS-Erweiterung](https://github.com/Microsoft/botbuilder-tools/tree/master/LUIS) enthalten, sodass Sie Ihre LUIS-Ressourcen unabhängig voneinander verwalten können. Sie steht als npm-Modul bereit, das Sie herunterladen können:

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

![LUIS init](media/bot-builder-tools/luis-init.png) 


Nachdem diese Datei generiert wurde, ist Ihre Anwendung in der Lage, die LUIS-JSON-Datei (aus LUDown generiert) mithilfe des folgenden Befehls über die Befehlszeilenschnittstelle zu verarbeiten.

```shell
luis import application --in luis-app.json | msbot connect luis --stdin
```

### <a name="connect-to-qna-from-the-cli"></a>Herstellen einer Verbindung mit QnA über die CLI

Im neuen Toolset ist eine [QnA-Erweiterung](https://github.com/Microsoft/botbuilder-tools/tree/master/QnAMaker) enthalten, sodass Sie Ihre LUIS-Ressourcen unabhängig voneinander verwalten können. Sie steht als npm-Modul bereit, das Sie herunterladen können.

```shell
npm install -g qnamaker
```
Mit dem QnA Maker-Tool können Sie Ihre Wissensdatenbank erstellen, aktualisieren, veröffentlichen, löschen und trainieren. Als ersten Schritt müssen Sie eine **QNAMAKERRC**-Datei erstellen, die erforderlich ist, um den Endpunkt für Ihren Dienst zu aktivieren. Sie können diese Datei ganz einfach erstellen, indem Sie **qnamaker init** ausführen und den Eingabeaufforderungen unter Bereitstellung Ihrer QnA Maker-Wissensdatenbank-ID folgen. 

```shell
qnamaker init 
```
![QnaMaker init](media/bot-builder-tools/qnamaker-init.png)

Nachdem Ihre QNAMAKERRC-Datei generiert wurde, können Sie eine Verbindung mit Ihrer QnA-Wissensdatenbank herstellen, um die JSON-/TSV-Datei der Wissensdatenbank (aus LUDown generiert) mithilfe des folgenden Befehls zu verarbeiten.

```shell
qnamaker create --in qnaKB.json --msbot | msbot connect qna --stdin
```

## <a name="references"></a>Referenzen
- [Bot Builder-Tools: Quellcode](https://github.com/Microsoft/botbuilder-tools)
- [MSBot](https://github.com/Microsoft/botbuilder-tools/tree/master/MSBot)
- [ChatDown](https://github.com/Microsoft/botbuilder-tools/tree/master/Chatdown)
- [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/Ludown)
- [Azure-CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)

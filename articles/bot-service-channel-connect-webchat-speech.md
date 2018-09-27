---
title: Aktivieren der Spracheingabe in Webchat | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie die Spracheingabe im Webchat-Steuerelement für einen Bot aktivieren, der mit dem Webchatkanal verbunden ist.
keywords: Sprache, Webchat, Stimme, Mikrofon, Audio
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 563fdd480ac5165d5301faa3ed43af3118d2f7d7
ms.sourcegitcommit: 3bf3dbb1a440b3d83e58499c6a2ac116fe04b2f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2018
ms.locfileid: "46707476"
---
# <a name="enable-speech-in-web-chat"></a>Aktivieren der Spracheingabe in Webchat
Sie können eine Sprachschnittstelle im Webchat-Steuerelement aktivieren. Benutzer interagieren mit der Sprachschnittstelle über das Mikrofon im Webchat-Steuerelement.

![Sprachbeispiel für Webchat](~/media/bot-service-channel-webchat/webchat-sample-speech.png)

Wenn der Benutzer eine Antwort nicht spricht, sondern eingibt, deaktiviert der Webchat die Sprachfunktion, und der Bot gibt anstelle einer gesprochenen Antwort nur eine Textantwort aus. Um gesprochene Antworten wieder zu aktivieren, kann der Benutzer beim nächsten Mal wieder das Mikrofon verwenden, um dem Bot zu antworten. Wenn das Mikrofon gerade eine Eingabe annimmt, wird es abgedunkelt bzw. ausgefüllt angezeigt. Wenn es ausgegraut ist, kann der Benutzer darauf klicken, um es zu aktivieren.

## <a name="prerequisites"></a>Voraussetzungen

  Vor dem Ausführen des Beispiels müssen Sie über ein Direct Line-Geheimnis oder -Token für den Bot verfügen, den Sie mit dem Webchat-Steuerelement ausführen möchten. 
  * Informationen zum Abrufen eines Direct Line-Geheimnisses, das Ihrem Bot zugeordnet ist, finden Sie unter [Connect a bot to Direct Line](bot-service-channel-connect-directline.md) (Herstellen einer Direct Line-Verbindung für den Bot).
  * Informationen zum Austauschen eines Geheimnisses gegen ein Token finden Sie unter [Generate a Direct Line token](rest-api/bot-framework-rest-direct-line-3-0-authentication.md) (Generieren eines Direct Line-Tokens).

## <a name="customizing-web-chat-for-speech"></a>Anpassen des Webchats für die Spracheingabe
Zum Aktivieren der Sprachfunktion im Webchat müssen Sie den JavaScript-Code anpassen, mit dem das Webchat-Steuerelement aufgerufen wird. Sie können den für die Spracheingabe aktivierten Webchat lokal ausprobieren, indem Sie die folgenden Schritte ausführen.

1. Laden Sie die [index.html-Beispieldatei](https://aka.ms/web-chat-speech-sample) herunter. <!-- this aka.ms link needs to be updated if the sample location changes -->
2. Bearbeiten Sie den Code in `index.html` gemäß dem Typ der Sprachunterstützung, die Sie hinzufügen möchten. Die verschiedenen Arten von Sprachimplementierungen sind unter [Aktivieren der Sprachdienste](#enable-speech-services) beschrieben. 
3. Starten Sie einen Webserver. Eine Möglichkeit ist hierbei die Verwendung von `npm http-server` an einer Node.js-Eingabeaufforderung.

   * Führen Sie diesen Befehl aus, um `http-server` global zu installieren, damit die Ausführung über die Befehlszeile ermöglicht wird:

     ```
     npm install http-server -g
     ```

   * Führen Sie diesen Befehl aus, um einen Webserver über Port 8000 aus dem Verzeichnis zu starten, in dem `index.html` enthalten ist:

     ```
     http-server -p 8000
     ```
4. Geben Sie für Ihren Browser `http://localhost:8000/samples?parameters` als Ziel an. Mit `http://localhost:8000/samples?s=YOURDIRECTLINESECRET` wird der Bot beispielsweise mit einem Direct Line-Geheimnis aufgerufen. Die Parameter, die in der Abfragezeichenfolge festgelegt werden können, sind in der folgenden Tabelle beschrieben:

   | Parameter | BESCHREIBUNG |
   |-----------|-------------|
   | s | Direct Line-Geheimnis. Informationen zum Abrufen eines Direct Line-Geheimnisses finden Sie unter [Connect a bot to Direct Line](bot-service-channel-connect-directline.md) (Herstellen einer Direct Line-Verbindung für den Bot). |
   | t | Direct Line-Token. Informationen zum Generieren dieses Tokens finden Sie unter [Generate a Direct Line token](rest-api/bot-framework-rest-direct-line-3-0-authentication.md) (Generieren eines Direct Line-Tokens). |
   | Domäne | Optional. Die URL eines alternativen Direct Line-Endpunkts.  |
   | webSocket | Optional. Legen Sie diesen Parameter auf „true“ fest, um Nachrichten per WebSocket zu empfangen. Der Standardwert ist `false`. |
   | userid | Optional. Die ID des Bot-Benutzers.  |
   | username | Optional. Der Benutzername des Bot-Benutzers.  |
   | botid | Optional. ID des Bots. |
   | botname | Optional. Name des Bots. |


## <a name="enable-speech-services"></a>Aktivieren der Sprachdienste
Die Anpassung ermöglicht Ihnen das Hinzufügen der Sprachfunktion auf folgende Arten:

* **Vom Browser bereitgestellte Sprachfunktion**: Verwenden Sie die Sprachfunktion, die in den Webbrowser integriert ist. Derzeit ist diese Funktion nur im Chrome-Browser verfügbar.
* **Verwenden des Bing-Spracherkennungsdiensts**: Sie können den Bing-Spracherkennungsdienst für die Spracherkennung und -synthese nutzen. Diese Art des Zugriffs auf die Sprachfunktion wird von vielen Browsern unterstützt. In diesem Fall wird die Verarbeitung nicht im Browser durchgeführt, sondern auf einem Server.
* **Erstellen eines benutzerdefinierten Spracherkennungsdiensts**: Sie können Ihre eigenen benutzerdefinierten Komponenten für die Spracherkennung und -synthese erstellen.

### <a name="browser-provided-speech"></a>Vom Browser bereitgestellte Sprachfunktion

Mit dem folgenden Code werden Komponenten für die Spracherkennung und -synthese instanziiert, die im Browser enthalten sind. Diese Methode zum Hinzufügen der Spracherkennung wird nicht von allen Browsern unterstützt. 

> [!NOTE] 
> Google Chrome unterstützt die Spracherkennung über den Browser. Unter Umständen wird das Mikrofon von Chrome in den folgenden Fällen aber blockiert:
> * Wenn die URL der Seite, die den Webchat enthält, nicht mit `https://` beginnt, sondern mit `http://`.
> * Wenn die URL eine lokale Datei ist, für die nicht `http://localhost:8000`, sondern `file://` verwendet wird.

[!code-js[Specify speech options to use in-browser speech (JavaScript)](./includes/code/bot-service-channel-connect-webchat-speech.js#BrowserSpeech)]

### <a name="bing-speech-service"></a>Bing-Spracherkennungsdienst

Mit dem folgenden Code werden Komponenten für die Spracherkennung und -synthese instanziiert, für die der Bing-Spracherkennungsdienst verwendet wird. Die Erkennung und Generierung von Sprache wird auf dem Server durchgeführt. Dieser Mechanismus wird in mehreren Browsern unterstützt. 

> [!TIP]
> Sie können die Spracherkennungsvorbereitung verwenden, um die Genauigkeit der Spracherkennung Ihres Bots zu verbessern, wenn Sie den Bing-Spracherkennungsdienst nutzen. Weitere Informationen finden Sie im Blogbeitrag unter [Speech Support in Bot Framework](https://blog.botframework.com/2017/06/26/Speech-To-Text) (Sprachunterstützung im Bot Framework).

[!code-js[Specify speech options to use the Bing Speech API (JavaScript)](./includes/code/bot-service-channel-connect-webchat-speech.js#BingSpeech)]

#### <a name="use-the-bing-speech-service-with-a-token"></a>Verwenden des Bing-Spracherkennungsdiensts mit einem Token

Sie haben auch die Möglichkeit, die Cognitive Services-Spracherkennung mit einem Token zu aktivieren. Das Token wird auf einem sicheren Back-End mit Ihrem API-Schlüssel generiert.

Im folgenden Beispielcode wird veranschaulicht, wie der Tokenabruf über ein sicheres Back-End erfolgt, um das Offenlegen des API-Schlüssels zu vermeiden.

[!code-js[Fetch a token to use with the Bing Speech API (JavaScript)](./includes/code/bot-service-channel-connect-webchat-speech.js#FetchToken)]

### <a name="custom-speech-service"></a>Benutzerdefinierter Spracherkennungsdienst

Sie können auch Ihre eigene benutzerdefinierte Spracherkennung (Implementierung von ISpeechRecognizer) oder Sprachsynthese (Implementierung von ISpeechSynthesis) bereitstellen. 

[!code-js[Fetch a token to use with a custom speech service (JavaScript)](./includes/code/bot-service-channel-connect-webchat-speech.js#CustomSpeechService)]

### <a name="pass-the-speech-options-to-web-chat"></a>Übergeben der Sprachoptionen an den Webchat

Mit dem folgenden Code werden die Sprachoptionen an das Webchat-Steuerelement übergeben:

[!code-js[Pass speech options to Web Chat (JavaScript)](./includes/code/bot-service-channel-connect-webchat-speech.js#PassSpeechOptionsToWebChat)]

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun die Sprachinteraktion mit dem Webchat aktivieren können, können Sie sich darüber informieren, wie Ihr Bot gesprochene Nachrichten erstellt und den Status des Mikrofons anpasst:
* [Hinzufügen von Spracheingabe zu Nachrichten (C#)](dotnet/bot-builder-dotnet-text-to-speech.md)
* [Hinzufügen von Spracheingabe zu Nachrichten (Node.js)](nodejs/bot-builder-nodejs-text-to-speech.md)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* Sie können den Quellcode für das Webchat-Steuerelement auf GitHub [herunterladen](https://github.com/Microsoft/BotFramework-WebChat).
* Die [Dokumentation zur Bing-Spracheingabe-API](https://docs.microsoft.com/azure/cognitive-services/speech/home) enthält weitere Informationen zur Bing-Spracheingabe-API.


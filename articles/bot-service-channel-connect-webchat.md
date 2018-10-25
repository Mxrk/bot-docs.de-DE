---
title: Verbinden eines Bots mit dem Webchatkanal | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie das Webchat-Steuerelement auf Ihrer Webseite für einen Bot aktivieren, der mit dem Webchatkanal verbunden ist.
keywords: Webchat, Botkanal, Webseite, geheimer Schlüssel, iframe, HTML
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 10/10/2018
ms.openlocfilehash: 1658a5cd8ba3fc4e9c34849e1550f64461ced292
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "50000147"
---
# <a name="connect-a-bot-to-web-chat"></a>Verbinden eines Bots mit dem Webchat

[!INCLUDE pre-release-label]

Wenn Sie [einen Bot mit dem Botdienst](bot-service-quickstart.md) erstellen, wird der Webchatkanal automatisch für Sie konfiguriert. Der Webchatkanal enthält das Webchat-Steuerelement. Mit diesem können Benutzer direkt auf der Webseite mit Ihrem Bot interagieren.

![Webchatbeispiel](./media/bot-service-channel-webchat/create-a-bot.png)

Der Webchatkanal im Bot Framework-Portal enthält alles Notwendige zum Einbetten des Webchat-Steuerelements in eine Webseite. Zur Nutzung des Webchat-Steuerelements müssen Sie nur den geheimen Schlüssel des Bots abrufen und das Steuerelement in eine Webseite einbetten.

## <a id="step-1"></a> Abrufen des geheimen Botschlüssels

1. Öffnen Sie Ihren Bot im [Azure-Portal](http://portal.azure.com), und klicken Sie auf das Blatt **Kanäle**.

2. Klicken Sie in der Zeile für den **Webchatkanal** auf **Bearbeiten**.  
![Webchatkanal](./media/bot-service-channel-webchat/bot-service-channel-list.png)

3. Klicken Sie unter **Secret keys** (Geheime Schlüssel) unter dem ersten Schlüssel auf **Anzeigen**.  
![Geheimer Schlüssel](./media/bot-service-channel-webchat/secret-key.png)

4. Kopieren Sie den **geheimen Schlüssel** und den **Einbettungscode**.

5. Klicken Sie auf **Fertig**.

## <a name="embed-the-web-chat-control-in-your-website"></a>Einbetten des Webchat-Steuerelements in Ihre Website

Sie können beim Einbetten des Webchat-Steuerelements in Ihre Website zwischen zwei Optionen wählen:

### <a name="option-1---keep-your-secret-hidden-exchange-your-secret-for-a-token-and-generate-the-embed"></a>Option 1: Veröffentlichen Sie Ihr Geheimnis nicht, tauschen Sie es gegen ein Token ein, und erstellen Sie den Einbettungscode.

Verwenden Sie diese Option, wenn Sie eine Server-zu-Server-Anforderung ausführen, um Ihr Webchatgeheimnis gegen ein temporäres Token einzutauschen, und wenn Sie es anderen Entwicklern erschweren möchten, den Bot in ihre Website einzubetten.

So tauschen Sie Ihr Geheimnis gegen ein Token ein und erstellen den Einbettungscode:

1. Senden Sie eine **GET**-Anforderung an `https://webchat.botframework.com/api/tokens`, und übergeben Sie das Webchatgeheimnis mithilfe des `Authorization`-Headers. Der `Authorization`-Header verwendet das `BotConnector`-Schema und enthält Ihr Geheimnis, wie in der unten stehenden Beispielanforderung gezeigt wird.

2. Die Antwort auf Ihre **GET**-Anforderung enthält das Token (in Anführungszeichen eingeschlossen), mit dem eine Konversation gestartet werden kann. Hierzu muss das Webchat-Steuerelement innerhalb eines **iframe**-Tags gerendert werden. Ein Token ist nur für eine Konversation gültig. Wenn Sie eine weitere Konversation starten möchten, müssen Sie ein neues Token erstellen.

3. Ändern Sie im **Einbettungscode** `iframe`, den Sie aus dem Webchatkanal im Bot Framework-Portal kopiert haben (siehe [Abrufen des geheimen Botschlüssels](#step-1) oben) den `s=`-Parameter in `t=`, und ersetzen Sie „YOUR_SECRET_HERE“ durch Ihr Token.

> [!NOTE]
> Tokens werden automatisch verlängert, bevor sie ablaufen. 

##### <a name="example-request"></a>Beispielanforderung

GET-Anforderung https://webchat.botframework.com/api/tokens Autorisierung: BotConnector YOUR_SECRET_HERE
```

##### Example response 

```response
"IIbSpLnn8sA.dBB.MQBhAFMAZwBXAHoANgBQAGcAZABKAEcAMwB2ADQASABjAFMAegBuAHYANwA.bbguxyOv0gE.cccJjH-TFDs.ruXQyivVZIcgvosGaFs_4jRj1AyPnDt1wk1HMBb5Fuw"
```

##### <a name="example-iframe-using-token"></a>Beispiel-iframe (mithilfe eines Tokens)

```html
<iframe src="https://webchat.botframework.com/embed/YOUR_BOT_ID?t=YOUR_TOKEN_HERE"></iframe>
```

##### <a name="example-html-code"></a>Beispiel-HTML-Code
```html
<!DOCTYPE html>
<html>
<body>
  <iframe id="chat" style="width: 400px; height: 400px;" src=''></iframe>
</body>
<script>

    var xhr = new XMLHttpRequest();
    xhr.open('GET', "https://webchat.botframework.com/api/tokens", true);
    xhr.setRequestHeader('Authorization', 'BotConnector ' + 'YOUR SECRET HERE');
    xhr.send();
    xhr.onreadystatechange = processRequest;

    function processRequest(e) {
      if (xhr.readyState == 4  && xhr.status == 200) {
        var response = JSON.parse(xhr.responseText);
        document.getElementById("chat").src="https://webchat.botframework.com/embed/lucas-direct-line?t="+response
      }
    }

  </script>
</html>
```

### <a id="option-2"></a> Option 2: Betten Sie das Webchat-Steuerelement mithilfe des Geheimnisses in Ihre Website ein.

Verwenden Sie diese Option, wenn Sie es anderen Entwicklern ermöglichen möchten, den Bot auf einfache Weise in ihre Websites einzubetten. 

> [!WARNING]
> Hierzu müssen Entwickler nur Ihren Einbettungscode kopieren.

So betten Sie den Bot durch die Angabe des Geheimnisses innerhalb des `iframe`-Tags in Ihre Website ein:

1. Kopieren Sie den **Einbettungscode** `iframe` aus dem Webchatkanal innerhalb des Bot Framework-Portals (siehe [Abrufen des geheimen Botschlüssels](#step-1) oben).

2. Ersetzen Sie im **Einbettungscode** „YOUR_SECRET_HERE“ durch den Wert des **geheimen Schlüssels**, den Sie von der gleichen Seite kopiert haben.

##### <a name="example-iframe-using-secret"></a>Beispiel-iframe (mithilfe eines Geheimnisses)

```html
<iframe src="https://webchat.botframework.com/embed/YOUR_BOT_ID?s=YOUR_SECRET_HERE"></iframe>
```

## <a name="style-the-web-chat-control"></a>Gestalten des Webchat-Steuerelements

Sie können die Größe des Webchat-Steuerelements ändern, indem Sie das `style`-Attribut von `iframe` verwenden, um `height` und `width` festzulegen.

```html
<iframe style="height:480px; width:402px" src="... SEE ABOVE ..."></iframe>
```

![Chatsteuerelement-Client](./media/chatwidget-client.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Sie können den Quellcode für das Webchat-Steuerelement auf GitHub [herunterladen](https://aka.ms/BotFramework-WebChat-V4).

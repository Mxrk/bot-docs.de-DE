---
title: Problembehandlung bei der Bot Framework-Authentifizierung | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Authentifizierungsfehler bei Ihrem Bot beheben.
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/17
ms.openlocfilehash: a64edda73832f4d3fff49b08b5eaf6792c021ece
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39303336"
---
# <a name="troubleshooting-bot-framework-authentication"></a>Problembehandlung bei der Bot Framework-Authentifizierung

Dieser Leitfaden hilft Ihnen bei der Behebung von Authentifizierungsproblemen mit Ihrem Bot. Dazu wird eine Reihe von Szenarien ausgewertet, um zu bestimmen, wo das Problem liegt. 

> [!NOTE]
> Um alle Schritte in diesem Leitfaden ausführen zu können, müssen Sie den [Bot Framework-Emulator][Emulator] herunterladen und verwenden. Außerdem benötigen Sie Zugriff auf die Registrierungseinstellungen des Bots im <a href="https://dev.botframework.com" target="_blank">Bot Framework-Portal</a>.

## <a id="PW"></a> App-ID und Kennwort

Die Botsicherheit wird mit der **Microsoft-App-ID** und dem **Microsoft-App-Kennwort** konfiguriert, die Sie beim Registrieren Ihres Bot bei Bot Framework erhalten. Diese Werte sind normalerweise in der Konfigurationsdatei des Bots angegeben und werden zum Abrufen von Zugriffstoken vom Microsoft-Kontodienst verwendet. 

[Registrieren Sie Ihren Bot](~/bot-service-quickstart-registration.md) (falls noch nicht geschehen), um eine **Microsoft-App-ID** und ein **Microsoft-App-Kennwort** zu erhalten, die vom Bot zur Authentifizierung verwendet werden können. 

> [!NOTE]
> Wie Sie **AppID** und **AppPassword** für Ihren Bot finden, ist unter [MicrosoftAppID und MicrosoftAppPassword](bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword) beschrieben.

## <a name="step-1-disable-security-and-test-on-localhost"></a>Schritt 1: Deaktivieren der Sicherheit und Testen auf Localhost

In diesem Schritt prüfen Sie, ob Ihr Bot bei deaktivierter Sicherheit auf Localhost verfügbar und funktionsfähig ist. 

> [!WARNING]
> Durch das Deaktivieren der Sicherheit für Ihren Bot können unbekannte Angreifer die Identität von Benutzern annehmen. Führen Sie das folgende Verfahren nur durch, wenn Sie in einer geschützten Debugumgebung arbeiten.

### <a id="disable-security-localhost"></a> Deaktivieren der Sicherheit

Zum Deaktivieren der Sicherheit für Ihren Bot bearbeiten Sie dessen Konfigurationseinstellungen, und entfernen Sie die Werte für App-ID und Kennwort. 

Wenn Sie das Bot Builder SDK für .NET verwenden, bearbeiten Sie die folgenden Einstellungen in der Datei „Web.config“:

```xml
<appSettings>
  <add key="MicrosoftAppId" value="" />
  <add key="MicrosoftAppPassword" value="" />
</appSettings>
```

Wenn Sie das Bot Builder SDK für Node.js verwenden, bearbeiten Sie die folgenden Werte (oder aktualisieren Sie die entsprechenden Umgebungsvariablen):

```javascript
var connector = new builder.ChatConnector({
  appId: null,
  appPassword: null
});
```

### <a name="test-your-bot-on-localhost"></a>Testen Ihres Bots auf Localhost 

Als Nächstes testen Sie Ihren Bot auf Localhost mit dem Bot Framework-Emulator.

1. Starten Sie Ihren Bot auf Localhost.
2. Starten Sie den Bot Framework-Emulator.
3. Stellen Sie über den Emulator eine Verbindung mit Ihrem Bot her.
    - Geben Sie `http://localhost:port-number/api/messages` in die Adressleiste des Emulators ein. Dabei entspricht **port-number** der Portnummer, die in dem Browser angezeigt wird, in dem Ihre Anwendung ausgeführt wird. 
    - Vergewissern Sie sich, dass die Felder **Microsoft-App-ID** und **Microsoft-App-Kennwort** beide leer sind.
    - Klicken Sie auf **Verbinden**.
4. Geben Sie zum Testen der Verbindung mit Ihrem Bot einen beliebigen Text in den Emulator ein, und drücken Sie die EINGABETASTE.

Wenn der Bot auf die Eingabe antwortet und keine Fehler im Chatfenster angezeigt werden, haben Sie sichergestellt, dass Ihr Bot bei deaktivierter Sicherheit auf Localhost verfügbar und funktionsfähig ist. Fahren Sie mit [Schritt 2](#step-2) fort.

Falls ein oder mehrere Fehler im Chatfenster angegeben sind, klicken Sie darauf, um Details anzuzeigen. Häufige Probleme sind beispielweise:

* In den Emulatoreinstellungen ist ein falscher Endpunkt für den Bot angegeben. Stellen Sie sicher, dass Sie die richtige Portnummer in der URL und den richtigen Pfad am Ende der URL (z.B. `/api/messages`) angegeben haben.
* In den Emulatoreinstellungen ist ein Botendpunkt angegeben, der mit `https` beginnt. Auf Localhost sollte der Endpunkt mit `http` beginnen.
* In den Emulatoreinstellungen ist ein Wert für das Feld **Microsoft-App-ID** und/oder das Feld **Microsoft-App-Kennwort** angegeben. Beide Felder sollten leer sein.
* Die Sicherheit für den Bot wurde nicht deaktiviert. [Stellen Sie sicher](#disable-security-localhost), dass der Bot keinen Wert für die App-ID oder das Kennwort angibt.

## <a id="step-2"></a> Schritt 2: Überprüfen von App-ID und Kennwort Ihres Bots

In diesem Schritt überprüfen Sie, ob die App-ID und das Kennwort, die von Ihrem Bot für die Authentifizierung verwendet werden, gültig sind. (Wenn Sie diese Werte nicht kennen, [rufen Sie sie jetzt ab](#PW).) 

> [!WARNING]
> Mit den folgenden Anweisungen wird die SSL-Überprüfung für `login.microsoftonline.com` deaktiviert. Führen Sie dieses Verfahren nur in einem sicheren Netzwerk aus. Es empfiehlt sich, das Kennwort Ihrer Anwendung anschließend zu ändern.

### <a name="issue-an-http-request-to-the-microsoft-login-service"></a>Ausgeben einer HTTP-Anforderung an den Microsoft-Anmeldedienst

Diese Anweisungen beschreiben, wie [cURL](https://curl.haxx.se/download.html) zum Ausgeben einer HTTP-Anforderung an den Microsoft-Anmeldedienst verwendet wird. Sie können ein alternatives Tool wie Postman verwenden, doch vergewissern Sie sich, dass die Anforderung dem Bot Framework-[Authentifizierungsprotokoll](~/rest-api/bot-framework-rest-connector-authentication.md) entspricht.

Um sicherzustellen, dass App-ID und Kennwort Ihres Bots gültig sind, geben Sie die folgende Anforderung mithilfe von **cURL**aus, und ersetzen Sie dabei `APP_ID` und `APP_PASSWORD` durch App-ID und Kennwort Ihres Bots.

```cmd
curl -k -X POST https://login.microsoftonline.com/botframework.com/oauth2/v2.0/token -d "grant_type=client_credentials&client_id=APP_ID&client_secret=APP_PASSWORD&scope=https%3A%2F%2Fapi.botframework.com%2F.default"
```

Diese Anforderung versucht, App-ID und Kennwort Ihres Bots gegen ein Zugriffstoken auszutauschen. Wenn die Anforderung erfolgreich ist, erhalten Sie eine JSON-Nutzlast, die unter anderem eine `access_token`-Eigenschaft enthält. 

```json
{"token_type":"Bearer","expires_in":3599,"ext_expires_in":0,"access_token":"eyJ0eXAJKV1Q..."}
```

Wenn die Anforderung erfolgreich ist, haben Sie sichergestellt, dass App-ID und Kennwort, die Sie in der Anforderung angegeben haben, gültig sind. Fahren Sie mit [Schritt 3](#step-3) fort.

Wenn als Reaktion auf die Anforderung ein Fehler angezeigt wird, sehen Sie sich die Antwort an, um die Ursache des Fehlers zu ermitteln. Wenn die Antwort angibt, dass die App-ID oder das Kennwort ungültig ist, rufen Sie die [richtigen Werte](#PW) aus dem Bot Framework-Portal ab, und geben Sie die Anforderung erneut mit den neuen Werten aus, um deren Gültigkeit zu bestätigen. 

## Schritt 3: Aktivieren der Sicherheit und Testen auf Localhost <a id="step-3"></a>

An diesem Punkt haben Sie sichergestellt, dass Ihr Bot bei deaktivierter Sicherheit auf Localhost verfügbar und funktionsfähig ist, und bestätigt, dass App-ID und Kennwort, die vom Bot für die Authentifizierung verwendet werden, gültig sind. In diesem Schritt prüfen Sie, ob Ihr Bot bei aktivierter Sicherheit auf Localhost verfügbar und funktionsfähig ist.

### <a id="enable-security-localhost"></a> Aktivieren der Sicherheit

Die Sicherheit Ihres Bots basiert auf Microsoft-Diensten, selbst wenn Ihr Bot nur auf Localhost ausgeführt wird. Zum Aktivieren der Sicherheit für Ihren Bot bearbeiten Sie dessen Konfigurationseinstellungen, und geben Sie für App-ID und Kennwort die Werte ein, die Sie in [Schritt 2](#step-2) geprüft haben.

Wenn Sie das Bot Builder SDK für .NET verwenden, tragen Sie die folgenden Einstellungen in die Datei „Web.config“ ein:

```xml
<appSettings>
  <add key="MicrosoftAppId" value="APP_ID" />
  <add key="MicrosoftAppPassword" value="PASSWORD" />
</appSettings>
```

Wenn Sie das Bot Builder SDK für Node.js verwenden, tragen Sie die folgenden Einstellungen ein (oder aktualisieren Sie die entsprechenden Umgebungsvariablen):

```javascript
var connector = new builder.ChatConnector({
  appId: 'APP_ID',
  appPassword: 'PASSWORD'
});
```

> [!NOTE]
> Wie Sie **AppID** und **AppPassword** für Ihren Bot finden, ist unter [MicrosoftAppID und MicrosoftAppPassword](bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword) beschrieben.

### <a name="test-your-bot-on-localhost"></a>Testen Ihres Bots auf Localhost 

Als Nächstes testen Sie Ihren Bot auf Localhost mit dem Bot Framework-Emulator.

1. Starten Sie Ihren Bot auf Localhost.
2. Starten Sie den Bot Framework-Emulator.
3. Stellen Sie über den Emulator eine Verbindung mit Ihrem Bot her.
    - Geben Sie `http://localhost:port-number/api/messages` in die Adressleiste des Emulators ein. Dabei entspricht **port-number** der Portnummer, die in dem Browser angezeigt wird, in dem Ihre Anwendung ausgeführt wird. 
    - Geben Sie die App-ID Ihres Bots in das Feld **Microsoft-App-ID** ein.
    - Geben Sie das Kennwort Ihres Bots in das Feld **Microsoft-App-Kennwort** ein.
    - Klicken Sie auf **Verbinden**.
4. Geben Sie zum Testen der Verbindung mit Ihrem Bot einen beliebigen Text in den Emulator ein, und drücken Sie die EINGABETASTE.

Wenn der Bot auf die Eingabe antwortet und keine Fehler im Chatfenster angezeigt werden, haben Sie sichergestellt, dass Ihr Bot bei aktivierter Sicherheit auf Localhost verfügbar und funktionsfähig ist.  Fahren Sie mit [Schritt 4](#step-4) fort.

Falls ein oder mehrere Fehler im Chatfenster angegeben sind, klicken Sie darauf, um Details anzuzeigen. Häufige Probleme sind beispielweise:

* In den Emulatoreinstellungen ist ein falscher Endpunkt für den Bot angegeben. Stellen Sie sicher, dass Sie die richtige Portnummer in der URL und den richtigen Pfad am Ende der URL (z.B. `/api/messages`) angegeben haben.
* In den Emulatoreinstellungen ist ein Botendpunkt angegeben, der mit `https` beginnt. Auf Localhost sollte der Endpunkt mit `http` beginnen.
* In den Emulatoreinstellungen enthält das Feld **Microsoft-App-ID** und/oder das Feld **Microsoft-App-Kennwort** keine gültigen Werte. Beide Felder sollten ausgefüllt sein, und jedes Feld sollte den entsprechenden Wert enthalten, den Sie in [Schritt 2](#step-2) geprüft haben.
* Die Sicherheit für den Bot wurde nicht aktiviert. [Stellen Sie sicher](#enable-security-localhost), dass in den Konfigurationseinstellungen des Bots Werte für App-ID und Kennwort angegeben sind.

## Schritt 4: Testen Ihres Bots in der Cloud <a id="step-4"></a>

An diesem Punkt haben Sie sichergestellt, dass Ihr Bot bei deaktivierter Sicherheit auf Localhost verfügbar und funktionsfähig ist, bestätigt, dass App-ID und Kennwort des Bots gültig sind, und haben sich vergewissert, dass Ihr Bot bei aktivierter Sicherheit auf Localhost verfügbar und funktionsfähig ist. In diesem Schritt stellen Sie Ihren Bot in der Cloud bereit und überprüfen, ob er dort bei aktivierter Sicherheit verfügbar und funktionsfähig ist. 

### <a name="deploy-your-bot-to-the-cloud"></a>Bereitstellen Ihres Bots in der Cloud

Bot Framework erfordert, dass über das Internet auf Bots zugegriffen werden kann. Deshalb müssen Sie Ihren Bot auf einer Cloudhostingplattform wie Azure bereitstellen. Stellen Sie sicher, dass Sie die Sicherheit für Ihren Bot vor der Bereitstellung aktivieren, wie es in [Schritt 3](#step-3) beschrieben ist.

> [!NOTE]
> Wenn Sie noch nicht über einen Cloudhostinganbieter verfügen, können Sie sich für ein <a href="https://azure.microsoft.com/en-us/free/" target="_blank">kostenloses Konto</a> registrieren. 

Wenn Sie Ihren Bot in Azure bereitstellen, wird automatisch SSL für Ihre Anwendung konfiguriert und dadurch der für Bot Framework erforderliche **HTTPS**-Endpunkt aktiviert. Wenn die Bereitstellung über einen anderen Cloudhostinganbieter erfolgt, stellen Sie sicher, dass Ihre Anwendung für SSL konfiguriert ist, damit der Bot über einen **HTTPS**-Endpunkt verfügt.

### <a name="test-your-bot"></a>Testen Ihres Bots 

Führen Sie die folgenden Schritte aus, um Ihren Bot mit aktivierter Sicherheit in der Cloud zu testen.

1. Vergewissern Sie sich, dass Ihr Bot erfolgreich bereitgestellt wurde und ausgeführt wird. 
2. Melden Sie sich beim <a href="https://dev.botframework.com" target="_blank">Bot Framework-Portal</a> an.
3. Klicken Sie auf **Meine Bots**.
4. Wählen Sie den Bot aus, den Sie testen möchten.
5. Klicken Sie auf **Testen**, um den Bot in einem eingebetteten Webchat-Steuerelement zu öffnen.
6. Geben Sie zum Testen der Verbindung mit Ihrem Bot einen beliebigen Text in das Webchat-Steuerelement ein, und drücken Sie die EINGABETASTE.

Wenn ein Fehler im Chatfenster angezeigt wird, verwenden Sie die Fehlermeldung, um die Ursache des Fehlers zu ermitteln. Häufige Probleme sind beispielweise: 

* Der **Messaging-Endpunkt**, der auf der Seite **Einstellungen** für Ihren Bot im Bot Framework-Portal angegeben ist, ist falsch. Stellen Sie sicher, dass Sie den richtigen Pfad am Ende der URL angegeben haben (z.B. `/api/messages`).
* Der **Messaging-Endpunkt**, der auf der Seite **Einstellungen** für Ihren Bot im Bot Framework-Portal angegeben ist, beginnt nicht mit `https` oder ist für Bot Framework nicht vertrauenswürdig. Ihr Bot muss über ein gültiges und für Ketten vertrauenswürdiges Zertifikat verfügen.
* Der Bot ist mit fehlenden oder falschen Werten für App-ID oder Kennwort konfiguriert. [Stellen Sie sicher](#enable-security-localhost), dass in den Konfigurationseinstellungen des Bots gültige Werte für App-ID und Kennwort angegeben sind.

Wenn der Bot auf angemessene Weise auf die Eingabe reagiert, haben Sie sichergestellt, dass Ihr Bot bei aktivierter Sicherheit in der Cloud verfügbar und funktionsfähig ist. An diesem Punkt kann Ihr Bot eine sichere [Verbindung mit einem Kanal herstellen](~/bot-service-manage-channels.md), z.B. Skype, Facebook Messenger, Direct Line usw.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Wenn nach Abschluss der oben genannten Schritte weiterhin Probleme auftreten, haben Sie folgende Möglichkeiten:

* [Debuggen Sie Ihren Bot in der Cloud](~/bot-service-debug-emulator.md) mit dem Bot Framework-Emulator und <a href="https://ngrok.com/" target="_blank">ngrok</a>.
* Verwenden Sie ein Proxyfunktionstool wie [Fiddler](https://www.telerik.com/fiddler), um den HTTPS-Datenverkehr zu und von Ihrem Bot zu überprüfen. *Fiddler ist kein Microsoft-Produkt.*
* Sehen Sie sich den [Leitfaden zur Authentifizierung für Bot Connector][BotConnectorAuthGuide] an, um Informationen über die von Bot Framework verwendeten Authentifizierungstechnologien zu erhalten.
* Erhalten Sie Hilfe aus anderen Quellen über die [Support][Support]-Ressourcen für Bot Framework. 

[BotConnectorAuthGuide]: ~/rest-api/bot-framework-rest-connector-authentication.md
[Support]: bot-service-resources-links-help.md
[Emulator]: bot-service-debug-emulator.md

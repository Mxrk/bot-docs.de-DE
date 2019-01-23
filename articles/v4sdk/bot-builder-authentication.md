---
title: Hinzufügen von Authentifizierung zu Ihrem Bot über Azure Bot Service | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Authentifizierungsfeatures von Azure Bot Service zum Hinzufügen von SSO (Einmaliges Anmelden) zu Ihrem Bot verwenden.
author: JonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: abs
ms.date: 10/30/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 3bfbcb27aa6e38792f96e0d3fe042f02f6e11083
ms.sourcegitcommit: d385ec5fe61c469ab17e6f21b4a0d50e5110d0fd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54298317"
---
# <a name="add-authentication-to-your-bot-via-azure-bot-service"></a>Hinzufügen von Authentifizierung zu Ihrem Bot über Azure Bot Service

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

In diesem Tutorial werden neue Funktionen für die Bot-Authentifizierung in Azure Bot Service verwendet und Features bereitgestellt, mit denen das Entwickeln eines Bots vereinfacht wird, der Benutzer für verschiedene Identitätsanbieter authentifiziert, z.B. Azure Active Directory. GitHub, Uber usw. Diese Updates führen auch eine verbesserte Benutzerfreundlichkeit ein, indem die _Überprüfung von magischem Code_ für einige Clients entfernt wird.

Zuvor musste Ihr Bot OAuth-Controller und Anmeldungslinks enthalten, die Zielclient-IDs und Geheimnisse speichern sowie die Verwaltung von Benutzertoken durchführen.
<!--
These capabilities were bundled in the BotAuth and AuthBot samples that are on GitHub.
-->

Nun müssen Bot-Entwickler nicht mehr die OAuth-Controller hosten oder den Token-Lebenszyklus verwalten, da all das nun von Azure Bot Service ausgeführt wird.

Sie enthält folgende Features:

- Verbesserungen an den Kanälen zur Unterstützung neuer Authentifizierungsfeatures, z.B. neue WebChat- und DirectLineJS-Bibliotheken, damit die Überprüfung von sechsstelligem magischem Code nicht mehr erforderlich ist.
- Verbesserungen am Hinzufügen, Löschen und Konfigurieren von Verbindungseinstellungen für verschiedene OAuth-Identitätsanbieter im Azure-Portal.
- Unterstützung einer Vielzahl von einsatzbereiten Identitätsanbietern, einschließlich Azure Active Directory (V1- und V2-Endpunkte), GitHub und mehr.
- Updates an den Bot Framework SDKs für C# und Node.js, um Token abrufen, OAuthCards erstellen und TokenResponse-Ereignisse verarbeiten zu können.
- Beispiele für die Erstellung eines Bots, der gegenüber Azure AD authentifiziert wird.

Sie können die Schritte in diesem Artikel auch verwenden, um solche Features einem vorhandenen Bot hinzuzufügen. Im Folgenden werden Beispiel-Bots aufgeführt, die die neuen Authentifizierungsfeatures veranschaulichen.

| Beispiel | BotBuilder-Version | BESCHREIBUNG |
|:---|:---:|:---|
| **Botauthentifizierung** ([C#](https://aka.ms/v4cs-bot-auth-sample) / [JS](https://aka.ms/v4js-bot-auth-sample)) | v4 | Veranschaulicht die OAuthCard-Unterstützung. |
| **Botauthentifizierung MSGraph** ([C#](https://aka.ms/v4cs-auth-msgraph-sample) / [JS](https://aka.ms/v4js-auth-msgraph-sample)) | v4 |  Veranschaulicht die Microsoft Graph-API-Unterstützung mit OAuth 2. |

> [!NOTE]
> Die Authentifizierungsfeatures funktionieren auch mit Bot Builder v3. Allerdings wird in diesem Artikel nur v4-Beispielcode behandelt.

Weitere Informationen und Unterstützung finden Sie unter [Bot Framework additional resources (Zusätzliche Ressourcen zu Bot Framework)](https://docs.microsoft.com/azure/bot-service/bot-service-resources-links-help).

## <a name="overview"></a>Übersicht

In diesem Tutorial wird ein Beispiel-Bot erstellt, der mithilfe eines V1- oder V2-Tokens eine Verbindung mit Microsoft Graph herstellt. Im Rahmen dieses Prozesses verwenden Sie Code aus dem GitHub-Repository [Microsoft/BotBuilder-Samples](https://github.com/Microsoft/BotBuilder-Samples). In diesem Tutorial wird die Einrichtung beschrieben (einschließlich der Botanwendung).

- **Erstellen des Bots und einer Authentifizierungsanwendung**
- **Vorbereiten des Bot-Beispielcodes**
- **Verwenden des Emulators zum Testen des Bots**

Damit Sie diese Schritte ausführen können, müssen Sie Folgendes installiert haben: Visual Studio 2017, npm, Node.js und Git. Außerdem sollten Sie mit Azure, OAuth 2.0 und der Bot-Entwicklung vertraut sein.

Wenn Sie fertig sind, verfügen Sie über einen Bot, der auf ein paar einfache Aufgaben für eine Azure AD-Anwendung reagieren kann, z.B. kann er E-Mails überprüfen und senden oder anzeigen, wer Sie sind und wer Ihr Manager ist. Hierfür verwendet Ihr Bot ein Token von einer Azure AD-Anwendung für die Microsoft.Graph-Bibliothek.

Im letzten Abschnitt werden Ausschnitte aus dem Code des Bots erläutert.

- **Hinweise zum Abrufen des Tokens**

## <a name="create-your-bot-and-an-authentication-application"></a>Erstellen des Bots und einer Authentifizierungsanwendung

Sie müssen einen Registrierungsbot erstellen, dem Sie Ihren Bot-Code bereitstellen. Außerdem müssen Sie eine Azure AD-Anwendung (entweder V1 oder V2) erstellen, um Ihrem Bot den Zugriff auf Office 365 zu genehmigen.

> [!NOTE]
> Diese Authentifizierungsfeatures funktionieren auch mit anderen Arten von Bots. In diesem Tutorial wird jedoch ein Bot verwendet, der nur für die Registrierung geeignet ist.

### <a name="register-an-application-in-azure-ad"></a>Registrieren einer Anwendung in Azure AD

Sie benötigen eine Azure AD-Anwendung, die Ihr Bot verwenden kann, um eine Verbindung mit der Microsoft Graph-API, Ihren eigenen durch Azure AD geschützten Ressourcen usw. herzustellen.

Für diesen Bot können Sie die V1- und V2-Endpunkte von Azure AD verwenden.
Informationen zu den Unterschieden zwischen den V1- und V2-Endpunkten finden Sie im [Vergleich von V1 und V2](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) und in der [Übersicht über die Azure AD V2.0-Endpunkte](https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview).

#### <a name="to-create-an-azure-ad-v1-application"></a>So erstellen Sie eine Azure AD V1-Anwendung

1. Wechseln Sie [im Azure-Portal zu Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview).
1. Klicken Sie auf **App-Registrierungen**.
1. Klicken Sie im Bereich **App-Registrierungen** auf **Registrierung einer neuen Anwendung**.
1. Füllen Sie die erforderlichen Felder aus, und erstellen Sie die App-Registrierung.
   1. Benennen Sie Ihre Anwendung.
   1. Legen Sie **Web-App/API** für den **Anwendungstyp** fest.
   1. Legen Sie `https://token.botframework.com/.auth/web/redirect` als **Anmelde-URL** fest.
   1. Klicken Sie auf **Create**.
      - Nach der Erstellung wird die App in einem Bereich **Registrierte App** angezeigt.
      - Notieren Sie sich die **Anwendungs-ID**. Diese müssen Sie später als _Client-ID_ angeben.
1. Klicken Sie auf **Einstellungen**, um Ihre Anwendung zu konfigurieren.
1. Klicken Sie auf **Schlüssel**, um den Bereich **Schlüssel** zu öffnen.
   1. Erstellen Sie unter **Kennwörter** einen `BotLogin`-Schlüssel.
   1. Legen Sie die **Dauer** auf **Läuft nie ab** fest.
   1. Klicken Sie auf **Speichern**, und notieren Sie sich den Schlüsselwert. Diesen geben Sie später für das _Anwendungsgeheimnis_ an.
   1. Schließen Sie den **Schlüssel**-Bereich.
1. Klicken Sie auf **Erforderliche Berechtigungen**, um den Bereich **Erforderliche Berechtigungen** zu öffnen.
   1. Klicken Sie auf **Hinzufügen**.
   1. Klicken Sie auf **API auswählen**, wählen Sie dann **Microsoft Graph** aus, und klicken Sie auf **Auswählen**.
   1. Klicken Sie auf **Berechtigungen auswählen**. Wählen Sie die Anwendungsberechtigungen aus, die Ihre Anwendung verwenden soll.

      > [!NOTE]
      > Für Berechtigungen, die mit **Administrator erforderlich** gekennzeichnet sind, müssen sich sowohl ein Benutzer als auch ein Mandantenadministrator anmelden, also sollten Sie diese für Ihren Bot vermeiden.

      Wählen Sie die folgenden delegierten Microsoft Graph-Berechtigungen aus:
      - Grundlegende Profile aller Benutzer lesen
      - Lesen von Benutzer-E-Mails
      - Anmelden und Benutzerprofil lesen
      - Senden von E-Mails als Benutzer
      - Anzeigen des grundlegenden Benutzerprofils
      - Anzeigen der E-Mail-Adresse des Benutzers

   1. Klicken Sie auf **Auswählen**, und klicken Sie dann auf **Fertig**.
   1. Schließen Sie den Bereich **Erforderliche Berechtigungen**.

Hiermit haben Sie die Azure AD V1-Anwendung konfiguriert.

#### <a name="to-create-an-azure-ad-v2-application"></a>So erstellen Sie eine Azure AD V2-Anwendung

1. Navigieren Sie zum [Anwendungsregistrierungsportal von Microsoft](https://apps.dev.microsoft.com).
1. Klicken Sie auf **App hinzufügen**.
1. Geben Sie der Azure AD-App einen Namen, und klicken Sie auf **Erstellen**.

    Notieren Sie sich die GUID der **Anwendungs-ID**. Diese geben Sie später als Client-ID für die Verbindungseinstellung an.

1. Klicken Sie unter **Anwendungsgeheimnisse** auf **Neues Kennwort generieren**.

    Notieren Sie sich das Kennwort, das im Popupelement angezeigt wird. Diese geben Sie später als geheimen Clientschlüssel für die Verbindungseinstellung an.

1. Klicken Sie unter **Plattformen** auf **Plattform hinzufügen**.
1. Klicken Sie im Popupelement **Plattform hinzufügen** auf **Web**.
    1. Lassen Sie das Kontrollkästchen **Impliziten Ablauf zulassen** aktiviert.
    1. Geben Sie `https://token.botframework.com/.auth/web/redirect` für die **Umleitungs-URL** ein.
    1. Lassen Sie die **Abmelde-URL** leer.
1. Unter **Microsoft Graph-Berechtigungen** können Sie zusätzliche delegierte Berechtigungen hinzufügen.
    - Fügen Sie für dieses Tutorial die Berechtigungen **Mail.Read**, **Mail.Send**, **openid**, **profile**, **User.Read** und **User.ReadBasic.All** hinzu.
      Der Bereich der Verbindungseinstellung benötigt **openid** und eine Ressource in Azure AD Graph, z.B. **Mail.Read**.
    - Notieren Sie sich die ausgewählten Berechtigungen. Diese geben Sie später als Bereiche für die Verbindungseinstellung an.

1. Klicken Sie unten auf der Seite auf **Speichern** .

### <a name="create-your-bot-on-azure"></a>Erstellen Ihres Bots in Azure

Erstellen Sie über das [Azure-Portal](https://portal.azure.com/) eine **bot channels registration** (Botkanalregistrierung).

### <a name="register-your-azure-ad-application-with-your-bot"></a>Registrieren der Azure AD-Anwendung mit dem Bot

Der nächste Schritt besteht darin, Ihren Bot mit der soeben erstellten Azure AD-Anwendung zu registrieren.

#### <a name="to-register-an-azure-ad-v1-application"></a>So registrieren Sie eine Azure AD V1-Anwendung

1. Navigieren Sie im [Azure-Portal](http://portal.azure.com/) zur Ressourcenseite Ihres Bots.
1. Klicken Sie auf **Einstellungen**.
1. Klicken Sie ganz unten Auf der Seite unter **OAuth-Verbindungseinstellungen** auf **Einstellung hinzufügen**.
1. Füllen Sie das Formular wie folgt aus:
    1. Geben Sie unter **Name** einen Namen für die Verbindung ein. Diesen verwenden Sie im Code Ihres Bots.
    1. Wählen Sie für **Dienstanbieter** die Option **Azure Active Directory** aus. Sobald Sie diese Option ausgewählt haben, werden Felder für Azure AD angezeigt.
    1. Geben Sie unter **Client-ID** die Anwendungs-ID ein, die Sie für Ihre Azure AD V1-Anwendung notiert haben.
    1. Geben Sie unter **Geheimer Clientschlüssel** den Schlüssel ein, den Sie für den `BotLogin`-Schlüssel Ihrer Anwendung notiert haben.
    1. Geben Sie `authorization_code` unter **Gewährungstyp** ein.
    1. Geben Sie `https://login.microsoftonline.com` unter **Anmelde-URL** ein.
    1. Geben Sie unter **Mandanten-ID** die Mandanten-ID für Azure Active Directory ein, z.B. `microsoft.com` oder `common`.

       Dieser Mandant wird den Benutzern zugeordnet, die authentifiziert werden können. Verwenden Sie den `common`-Mandanten, um allen zu ermöglichen, sich über den Bot selbst zu authentifizieren.

    1. Geben Sie `https://graph.microsoft.com/` als **Ressourcen-URL** ein.
    1. Lassen Sie das Feld **Bereiche** leer.
1. Klicken Sie auf **Speichern**.

> [!NOTE]
> Mit diesen Werten kann Ihre Anwendung über die Microsoft Graph-API auf Office 365-Daten zugreifen.

Sie können diesen Verbindungsnamen nun im Code Ihres Bots verwenden, um Benutzertoken abzurufen.

#### <a name="to-register-an-azure-ad-v2-application"></a>So registrieren Sie eine Azure AD V2-Anwendung

1. Navigieren Sie im [Azure-Portal](http://portal.azure.com/) zur Seite für die Bot-Kanalregistrierung Ihres Bots.
1. Klicken Sie auf **Einstellungen**.
1. Klicken Sie ganz unten Auf der Seite unter **OAuth-Verbindungseinstellungen** auf **Einstellung hinzufügen**.
1. Füllen Sie das Formular wie folgt aus:
    1. Geben Sie unter **Name** einen Namen für die Verbindung ein. Diesen verwenden Sie im Code Ihres Bots.
    1. Wählen Sie für **Dienstanbieter** die Option **Azure Active Directory V2** aus. Sobald Sie diese Option ausgewählt haben, werden Felder für Azure AD angezeigt.
    1. Geben Sie unter **Client-ID** Ihre Azure AD V2-Anwendungs-ID aus der Anwendungsregistrierung ein.
    1. Geben Sie unter **Client-ID** Ihr Azure AD V2-Anwendungskennwort aus der Anwendungsregistrierung ein.
    1. Geben Sie unter **Mandanten-ID** die Mandanten-ID für Azure Active Directory ein, z.B. `microsoft.com` oder `common`.

        Dieser Mandant wird den Benutzern zugeordnet, die authentifiziert werden können. Verwenden Sie den `common`-Mandanten, um allen zu ermöglichen, sich über den Bot selbst zu authentifizieren.

    1. Geben Sie unter **Bereich** die Namen der Berechtigungen ein, die Sie in der Anwendungsregistrierung ausgewählt haben: `Mail.Read Mail.Send openid profile User.Read User.ReadBasic.All`.

        > [!NOTE]
        > Für Azure AD V2 verwendet **Bereiche** eine Liste aus durch Leerzeichen getrennten Werten unter Beachtung der Groß-/Kleinschreibung.

1. Klicken Sie auf **Speichern**.

> [!NOTE]
> Mit diesen Werten kann Ihre Anwendung über die Microsoft Graph-API auf Office 365-Daten zugreifen.

Sie können diesen Verbindungsnamen nun im Code Ihres Bots verwenden, um Benutzertoken abzurufen.

#### <a name="to-test-your-connection"></a>So testen Sie Ihre Verbindung

1. Öffnen Sie die Verbindung, die Sie gerade erstellt haben.
1. Klicken Sie oben im Bereich **Service Provider Connection Setting** (Dienstanbieter-Verbindungseinstellung) auf **Verbindung testen**.
1. Beim ersten Mal sollte dies eine neue Registerkarte im Browser öffnen, auf der die Berechtigungen aufgeführt werden, die die App von Ihnen anfordert.
1. Klicken Sie auf **Annehmen**.
1. Daraufhin werden Sie zu einer Seite **Test Connection to <your-connection-name> Succeeded** (Testverbindung mit <Name-Ihrer-Verbindung> erfolgreich) umgeleitet.

## <a name="prepare-the-bot-sample-code"></a>Vorbereiten des Bot-Beispielcodes

Je nach gewähltem Beispiel verwenden Sie entweder C# oder Node.

1. Klicken Sie oben auf einen der Links zu den Beispielen, und klonen Sie das GitHub-Repository.
1. Befolgen Sie die Anleitung auf der Seite mit der GitHub-Infodatei zum Ausführen des jeweiligen Bots (C# oder Node).
1. Bei Verwendung des Beispiels für die C#-Bot-Authentifizierung:
    1. Legen Sie die Variable `ConnectionName` in der Datei `AuthenticationBot.cs` auf den Wert fest, den Sie beim Konfigurieren der OAuth 2.0-Verbindungseinstellung Ihres Bots verwendet haben.
    1. Legen Sie den Wert `appId` in der Datei `BotConfiguration.bot` auf die App-ID Ihres Bots fest.
    1. Legen Sie den Wert `appPassword` in der Datei `BotConfiguration.bot` auf das Geheimnis Ihres Bots fest.
1. Bei Verwendung des Beispiels für die Node/JS-Bot-Authentifizierung:
    1. Legen Sie die Variable `CONNECTION_NAME` in der Datei `bot.js` auf den Wert fest, den Sie beim Konfigurieren der OAuth 2.0-Verbindungseinstellung Ihres Bots verwendet haben.
    1. Legen Sie den Wert `appId` in der Datei `bot-authentication.bot` auf die App-ID Ihres Bots fest.
    1. Legen Sie den Wert `appPassword` in der Datei `bot-authentication.bot` auf das Geheimnis Ihres Bots fest.

    > [!IMPORTANT]
    > Abhängig von den Zeichen in Ihrem Geheimnis müssen Sie das Kennwort möglicherweise mit XML-Escapezeichen versehen. Beispielsweise müssen alle kaufmännische Und-Zeichen (&) als `&amp;` codiert werden.

    ```json
    {
        "name": "BotAuthentication",
        "secretKey": "",
        "services": [
            {
            "appId": "",
            "id": "http://localhost:3978/api/messages",
            "type": "endpoint",
            "appPassword": "",
            "endpoint": "http://localhost:3978/api/messages",
            "name": "BotAuthentication"
            }
        ]
    }
    ```

    Wenn Sie nicht wissen, wie Sie die Werte für Ihre **Microsoft-App-ID** und das **Microsoft-App-Kennwort** abrufen, sehen Sie sich die **ApplicationSettings**-Datei von Azure App Service an, die im Azure-Portal für Ihren Bot bereitgestellt wurde.

    > [!NOTE]
    > Sie können den Code für diesen Bot jetzt wieder für Ihr Azure-Abonnement freigeben (klicken Sie hierzu mit der rechten Maustaste auf das Projekt, und klicken Sie dann auf **Veröffentlichen**), für dieses Tutorial müssen Sie das aber nicht tun. Sie müssten eine Veröffentlichungskonfiguration einrichten, die den Anwendungs- und Hostingplan verwendet, den Sie beim Konfigurieren des Bots im Azure-Portal verwendet haben.

## <a name="use-the-emulator-to-test-your-bot"></a>Verwenden des Emulators zum Testen des Bots

Sie müssen den [Bot-Emulator](https://github.com/Microsoft/BotFramework-Emulator) installieren, um den Bot lokal zu testen. Sie können den V3- oder V4-Emulator verwenden.

1. Starten Sie Ihren Bot (mit oder ohne Debuggen).
1. Notieren Sie sich die Localhost-Portnummer der Seite. Sie benötigen diese Information für die Interaktion mit Ihrem Bot.
1. Starten Sie den Emulator.
1. Stellen Sie eine Verbindung mit dem Bot her. Stellen Sie sicher, dass in der Botkonfiguration bei Verwendung von Authentifizierung die **Microsoft-App-ID** und das **Microsoft-App-Kennwort** verwendet werden.
1. Vergewissern Sie sich, dass in den Emulator-Einstellungen die Option **Use a sign-in verification code for OAuthCards** (Anmeldeprüfcode für OAuthCards verwenden) ausgewählt und **ngrok** aktiviert ist, damit Azure Bot Service das Token bei seiner Verfügbarkeit an den Emulator zurückgeben kann.

   Wenn Sie die Verbindung noch nicht konfiguriert haben, geben Sie die Adresse sowie die Microsoft-App-ID und das Kennwort Ihres Bots an. Fügen Sie `/api/messages` zur URL des Bots hinzu. Ihre URL sollte ungefähr wie folgt aussehen: `http://localhost:portNumber/api/messages`.

1. Geben Sie `help` ein, um eine Liste der verfügbaren Befehle für den Bot anzuzeigen und die Authentifizierungsfeatures zu testen.
1. Sobald Sie sich angemeldet haben, müssen Sie Ihre Anmeldeinformationen nur neu eingeben, wenn Sie sich abgemeldet haben.
1. Geben Sie `signout` ein, um sich abzumelden und die Authentifizierung abzubrechen.

<!--To restart completely from scratch you also need to:
1. Navigate to the **AppData** folder for your account.
1. Go to the **Roaming/botframework-emulator** subfolder.
1. Delete the **Cookies** and **Coolies-journal** files.
-->

> [!NOTE]
> Für die Bot-Authentifizierung ist die Verwendung des Bot Connector-Diensts erforderlich. Da der Dienst auf die Informationen der Botkanalregistrierung für Ihren Bot zugreift, müssen Sie den Messaging-Endpunkt des Bots im Portal festlegen. Für die Authentifizierung ist außerdem die Verwendung von HTTPS erforderlich, weshalb Sie eine HTTPS-Weiterleitungsadresse für Ihren lokalen Bot erstellen mussten.

<!--The following is necessary for WebChat:
1. Use the **ngrok** command-line tool to get a forwarding HTTPS address for your bot.
   - For information on how to do this, see [Debug any Channel locally using ngrok](https://blog.botframework.com/2017/10/19/debug-channel-locally-using-ngrok/).
   - Any time you exit **ngrok**, you will need to redo this and the following step before starting the Emulator.
1. On the Azure Portal, go to the **Settings** blade for your bot.
   1. In the **Configuration** section, change the **Messaging endpoint** to the HTTPS forwarding address generated by **ngrok**.
   1. Click **Save** to save your change.
-->

## <a name="notes-on-the-token-retrieval-flow"></a>Hinweise zum Abrufen des Tokens

Wenn ein Benutzer den Bot zu einem Vorgang auffordert, für den der Benutzer angemeldet sein muss, kann der Bot ein `OAuthPrompt`-Element verwenden, um das Abrufen eines Tokens für eine bestimmte Verbindung zu initiieren. Mit `OAuthPrompt` wird ein Fluss für den Tokenabruf mit folgenden Elementen erstellt:

1. Es wird überprüft, ob Azure Bot Service bereits über ein Token für den aktuellen Benutzer und die Verbindung verfügt. Wenn ja, wird das Token zurückgegeben.
1. Falls für Azure Bot Service noch kein zwischengespeichertes Token vorhanden ist, wird ein `OAuthCard`-Element erstellt. Hierbei handelt es sich um eine Schaltfläche, auf die der Benutzer klicken kann.
1. Nachdem der Benutzer auf die `OAuthCard`-Anmeldeschaltfläche geklickt hat, sendet Azure Bot Service das Token des Benutzers entweder direkt an den Bot oder zeigt einen sechsstelligen Authentifizierungscode für den Benutzer an, der im Chatfenster eingegeben werden muss.
1. Wenn für den Benutzer ein Authentifizierungscode angezeigt wird, tauscht der Bot diesen Authentifizierungscode gegen das Token des Benutzers aus.

Die nächsten beiden Codeausschnitte stammen aus dem `OAuthPrompt`-Element und veranschaulichen, wie diese Schritte in der Eingabeaufforderung funktionieren.

### <a name="check-for-a-cached-token"></a>Prüfen nach einem zwischengespeicherten Token

In diesem Code führt der Bot zunächst eine schnelle Überprüfung durch, um zu ermitteln, ob Azure Bot Service bereits über ein Token für den Benutzer (der vom aktuellen Aktivitätsabsender identifiziert wird) und den jeweiligen Verbindungsnamen (der in der Konfiguration verwendet wird) verfügt. Entweder Azure Bot Service enthält bereits ein zwischengespeichertes Token oder nicht. Mit dem Aufruf von GetUserTokenAsync wird diese schnelle Überprüfung durchgeführt. Wenn Azure Bot Service über ein Token verfügt und dieses zurückgibt, kann es sofort verwendet werden. Wenn Azure Bot Service über kein Token verfügt, gibt diese Methode NULL zurück. In diesem Fall kann der Bot eine angepasste OAuth-Karte senden, über die der Benutzer sich anmelden kann.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// First ask Bot Service if it already has a token for this user
var token = await adapter.GetUserTokenAsync(turnContext, connectionName, null, cancellationToken).ConfigureAwait(false);
if (token != null)
{
    // use the token to do exciting things!
}
else
{
    // If Bot Service does not have a token, send an OAuth card to sign in
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
public async getUserToken(context: TurnContext, code?: string): Promise<TokenResponse|undefined> {
    // Get the token and call validator
    const adapter: any = context.adapter as any; // cast to BotFrameworkAdapter
    return await adapter.getUserToken(context, this.settings.connectionName, code);
}
```

---

### <a name="send-an-oauthcard-to-the-user"></a>Senden einer OAuth-Karte an den Benutzer

Sie können die OAuth-Karte mit beliebigem Text und Schaltflächentext anpassen. Folgendes ist hier wichtig:

- Legen Sie `ContentType` auf `OAuthCard.ContentType` fest.
- Legen Sie für die `ConnectionName`-Eigenschaft den Namen der Verbindung fest, die Sie verwenden möchten.
- Fügen Sie eine Schaltfläche mit einer Kartenaktion (`CardAction`) vom Typ (`Type`) `ActionTypes.Signin` ein. Beachten Sie, dass Sie keinen Wert für den Anmeldelink angeben müssen.

Am Ende dieses Aufrufs muss der Bot darauf „warten“, dass das Token zurückgegeben wird. Dieser Wartevorgang findet auf dem Hauptdatenstrom von Aktivitäten statt, da der Benutzer möglicherweise viel tun muss, um sich anzumelden.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
private async Task SendOAuthCardAsync(ITurnContext turnContext, IMessageActivity message, CancellationToken cancellationToken = default(CancellationToken))
{
    if (message.Attachments == null)
    {
        message.Attachments = new List<Attachment>();
    }

    message.Attachments.Add(new Attachment
    {
        ContentType = OAuthCard.ContentType,
        Content = new OAuthCard
        {
            Text = "Please sign in",
            ConnectionName = connectionName,
            Buttons = new[]
            {
                new CardAction
                {
                    Title = "Sign In",
                    Text = "Sign In",
                    Type = ActionTypes.Signin,
                },
            },
        },
    });

    await turnContext.SendActivityAsync(message, cancellationToken).ConfigureAwait(false);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
private async sendOAuthCardAsync(context: TurnContext, prompt?: string|Partial<Activity>): Promise<void> {
    // Initialize outgoing message
    const msg: Partial<Activity> =
        typeof prompt === 'object' ? {...prompt} : MessageFactory.text(prompt, undefined, InputHints.ExpectingInput);
    if (!Array.isArray(msg.attachments)) { msg.attachments = []; }

    const cards: Attachment[] = msg.attachments.filter((a: Attachment) => a.contentType === CardFactory.contentTypes.oauthCard);
    if (cards.length === 0) {
        // Append oauth card
        msg.attachments.push(CardFactory.oauthCard(
            this.settings.connectionName,
            this.settings.title,
            this.settings.text
        ));
    }

    // Send prompt
    await context.sendActivity(msg);
}
```

---

### <a name="wait-for-a-tokenresponseevent"></a>Warten auf TokenResponseEvent

In diesem Code wartet der Bot auf `TokenResponseEvent` (weitere Informationen zur Weiterleitung an den Dialogstapel finden Sie unten). Die `WaitForToken`-Methode ermittelt zunächst, ob dieses Ereignis gesendet wurde. Wenn es gesendet wurde, kann der Bot es verwenden. Wenn nicht, übergibt die `RecognizeTokenAsync`-Methode den Text, der an den Bot gesendet wurde, an `GetUserTokenAsync`. Der Grund hierfür besteht darin, dass einige Clients (z.B. WebChat) keinen Code zum Überprüfen von magischem Code benötigen und das Token deshalb direkt in `TokenResponseEvent` senden. Für andere Clients ist der magische Code weiterhin erforderlich (z.B. Facebook oder Slack). Azure Bot Service sendet diesen Clients einen sechsstelligen magischen Code und fordert Benutzer dazu auf, diesen in das Chatfenster einzugeben. Dieses Fallbackverhalten ist zwar nicht ideal, aber wenn `RecognizeTokenAsync` Code empfängt, kann der Bot diesen Code an Azure Bot Service senden und ein Token zurückerhalten. Wenn dieser Aufruf ebenfalls fehlschlägt, können Sie entscheiden, ob Sie einen Fehler melden oder auf andere Weise reagieren möchten. In den meisten Fällen sollte der Bot jedoch nun über ein Benutzertoken verfügen.

Wenn Sie sich den Botcode der Beispiele ansehen, fällt Ihnen auf, dass `Event`- und `Invoke`-Aktivitäten auch an den Dialogstapel weitergeleitet werden.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// This can be called when the bot receives an Activity after sending an OAuthCard
private async Task<TokenResponse> RecognizeTokenAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
{
    if (IsTokenResponseEvent(turnContext))
    {
        // The bot received the token directly
        var tokenResponseObject = turnContext.Activity.Value as JObject;
        var token = tokenResponseObject?.ToObject<TokenResponse>();
        return token;
    }
    else if (IsTeamsVerificationInvoke(turnContext))
    {
        var magicCodeObject = turnContext.Activity.Value as JObject;
        var magicCode = magicCodeObject.GetValue("state")?.ToString();

        var token = await adapter.GetUserTokenAsync(turnContext, _settings.ConnectionName, magicCode, cancellationToken).ConfigureAwait(false);
        return token;
    }
    else if (turnContext.Activity.Type == ActivityTypes.Message)
    {
        // make sure it's a 6-digit code
        var matched = _magicCodeRegex.Match(turnContext.Activity.Text);
        if (matched.Success)
        {
            var token = await adapter.GetUserTokenAsync(turnContext, _settings.ConnectionName, matched.Value, cancellationToken).ConfigureAwait(false);
            return token;
        }
    }

    return null;
}

private bool IsTokenResponseEvent(ITurnContext turnContext)
{
    var activity = turnContext.Activity;
    return activity.Type == ActivityTypes.Event && activity.Name == "tokens/response";
}

private bool IsTeamsVerificationInvoke(ITurnContext turnContext)
{
    var activity = turnContext.Activity;
    return activity.Type == ActivityTypes.Invoke && activity.Name == "signin/verifyState";
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
private async recognizeToken(context: TurnContext): Promise<PromptRecognizerResult<TokenResponse>> {
    let token: TokenResponse|undefined;
    if (this.isTokenResponseEvent(context)) {
        token = context.activity.value as TokenResponse;
    } else if (this.isTeamsVerificationInvoke(context)) {
        const code: any = context.activity.value.state;
        await context.sendActivity({ type: 'invokeResponse', value: { status: 200 }});
        token = await this.getUserToken(context, code);
    } else if (context.activity.type === ActivityTypes.Message) {
        const matched: RegExpExecArray = /(\d{6})/.exec(context.activity.text);
        if (matched && matched.length > 1) {
            token = await this.getUserToken(context, matched[1]);
        }
    }

    return token !== undefined ? { succeeded: true, value: token } : { succeeded: false };
}

private isTokenResponseEvent(context: TurnContext): boolean {
    const activity: Activity = context.activity;
    return activity.type === ActivityTypes.Event && activity.name === 'tokens/response';
}

private isTeamsVerificationInvoke(context: TurnContext): boolean {
    const activity: Activity = context.activity;
    return activity.type === ActivityTypes.Invoke && activity.name === 'signin/verifyState';
}
```

---

### <a name="message-controller"></a>Message-Controller

Beachten Sie, dass das Token von diesem Beispiel-Bot bei aufeinanderfolgenden Aufrufen nie zwischengespeichert werden. Der Grund hierfür ist, dass der Bot das Token immer von Azure Bot Service anfragen kann. Damit wird vermieden, dass der Bot unter anderem den Lebenszyklus des Tokens verwalten und das Token aktualisieren muss, da Azure Bot Service sich darum kümmert.

## <a name="additional-resources"></a>Zusätzliche Ressourcen
[Bot Framework SDK](https://github.com/microsoft/botbuilder)

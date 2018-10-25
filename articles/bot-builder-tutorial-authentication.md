---
title: Hinzufügen von Authentifizierung zu Ihrem Bot über Azure Bot Service | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Authentifizierungsfeatures von Azure Bot Service zum Hinzufügen von SSO (Einmaliges Anmelden) zu Ihrem Bot verwenden.
author: JonathanFingold
ms.author: JonathanFingold
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 10/04/2018
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 9b9a3594e3a1f6a93ce3d9b3314880c78b88a9c5
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49998907"
---
[!INCLUDE [pre-release-label](includes/pre-release-label-v3.md)]

# <a name="add-authentication-to-your-bot-via-azure-bot-service"></a>Hinzufügen von Authentifizierung zu Ihrem Bot über Azure Bot Service
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
- Updates an den Bot Builder SDKs für C# und Node.js, um Token abrufen, OAuthCards erstellen und TokenResponse-Ereignisse verarbeiten zu können.
- Beispiele zum Erstellen eines Bots, der mit Azure Active Directory (V1- und V2-Endpunkte) und mit GitHub authentifiziert wird.

Sie können die Schritte in diesem Artikel auch verwenden, um solche Features einem vorhandenen Bot hinzuzufügen. Im Folgenden werden Beispiel-Bots aufgeführt, die die neuen Authentifizierungsfeatures veranschaulichen.

| Beispiel | BotBuilder-Version | BESCHREIBUNG |
|:---|:---:|:---|
| [AadV1Bot](https://aka.ms/AadV1Bot) | V3 | Veranschaulicht die OAuthCard-Unterstützung im C# SDK V3 unter Verwendung des V1-Endpunkts von Azure AD |
| [AadV2Bot](https://aka.ms/AadV2Bot) | V3 |  Veranschaulicht die OAuthCard-Unterstützung im C# SDK V3 unter Verwendung des V2-Endpunkts von Azure AD |
| [GitHubBot](https://aka.ms/GitHubBot) | V3 |  Veranschaulicht die OAuthCard-Unterstützung im C# SDK V3 unter Verwendung von GitHub |
| [BasicOAuth](https://aka.ms/BasicOAuth) | V3 |  Veranschaulicht die OAuth 2.0-Unterstützung im C# SDK V3 |

> [!NOTE]
> Die Authentifizierungsfeatures funktionieren auch mit Node.js mit BotBuilder V3. Allerdings wird in diesem Artikel nur C#-Beispielcode behandelt.

Weitere Informationen und Unterstützung finden Sie unter [Bot Framework additional resources (Zusätzliche Ressourcen zu Bot Framework)](https://docs.microsoft.com/azure/bot-service/bot-service-resources-links-help).

## <a name="overview"></a>Übersicht

In diesem Tutorial wird ein Beispiel-Bot erstellt, der mithilfe eines V1- oder V2-Tokens eine Verbindung mit Microsoft Graph herstellt. <!--verify this info and fix wording--> Im Rahmen dieses Vorgangs verwenden Sie Code aus einem GitHub-Repository. In diesem Tutorial wird beschrieben, wie Sie dieses einschließlich der Bot-Anwendung einrichten.

- [Erstellen des Bots und einer Authentifizierungsanwendung](#create-your-bot-and-an-authentication-application)
- [Vorbereiten des Bot-Beispielcodes](#prepare-the-bot-sample-code)
- [Verwenden des Emulators zum Testen des Bots](#use-the-emulator-to-test-your-bot)

Damit Sie diese Schritte ausführen können, müssen Sie Folgendes installiert haben: Visual Studio 2017, npm, Node.js und Git. Außerdem sollten Sie mit Azure, OAuth 2.0 und der Bot-Entwicklung vertraut sein.

Wenn Sie fertig sind, verfügen Sie über einen Bot, der auf ein paar einfache Aufgaben für eine Azure AD-Anwendung reagieren kann, z.B. kann er E-Mails überprüfen und senden oder anzeigen, wer Sie sind und wer Ihr Manager ist. Hierfür verwendet Ihr Bot ein Token von einer Azure AD-Anwendung für die Microsoft.Graph-Bibliothek.

Im letzten Abschnitt werden Ausschnitte aus dem Code des Bots erläutert.

- [Hinweise zum Abrufen des Tokens](#notes-on-the-token-retrieval-flow)

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
1. Daraufhin werden Sie auf die Seite **Test Connection to `<your-connection-name>' Succeeded** (Testverbindung mit „<Name-Ihrer-Verbindung>“ erfolgreich) weitergeleitet.

## <a name="prepare-the-bot-sample-code"></a>Vorbereiten des Bot-Beispielcodes

1. Klonen Sie das GitHub-Repository unter https://github.com/Microsoft/BotBuilder.
1. Öffnen und erstellen Sie die Projektmappe `BotBuilder\CSharp\Microsoft.Bot.Builder.sln`.
1. Schließen Sie die Projektmappe, und öffnen Sie `BotBuilder\CSharp\Samples\Microsoft.Bot.Builder.Samples.sln`.
1. Legen Sie das Startprojekt fest.
    - Verwenden Sie das `Microsoft.Bot.Sample.AadV1Bot`-Projekt für Bots, die die Azure AD V1-Anwendung verwenden.
    - Verwenden Sie das `Microsoft.Bot.Sample.AadV2Bot`-Projekt für Bots, die die Azure AD V2-Anwendung verwenden.
1. Öffnen Sie die `Web.config`-Datei, und bearbeiten Sie wie folgt die Anwendungseinstellung:
    1. Legen Sie für `ConnectionName` den Wert fest, den Sie beim Konfigurieren der OAuth 2.0-Verbindungseinstellung Ihres Bots verwendet haben.
    1. Legen Sie für `MicrosoftAppId` die App-ID Ihres Bots fest.
    1. Legen Sie für `MicosoftAppPassword` das Geheimnis Ihres Bots fest.

    > [!IMPORTANT]
    > Abhängig von den Zeichen in Ihrem Geheimnis müssen Sie das Kennwort möglicherweise mit XML-Escapezeichen versehen. Beispielsweise müssen alle kaufmännische Und-Zeichen (&) als `&amp;` codiert werden.

    ```xml
    <appSettings>
        <add key="ConnectionName" value="<your-AAD-connection-name>"/>
        <add key="MicrosoftAppId" value="<your-bot-appId>" />
        <add key="MicrosoftAppPassword" value="<your-bot-password>" />
    </appSettings>
    ```

    Wenn Sie nicht wissen, wie Sie die Werte für Ihre **Microsoft-App-ID** und das **Microsoft-App-Kennwort** abrufen, sehen Sie sich die **ApplicationSettings**-Datei von Azure App Service an, die im Azure-Portal für Ihren Bot bereitgestellt wurde.

    > [!NOTE]
    > Sie können den Code für diesen Bot jetzt wieder für Ihr Azure-Abonnement freigeben (klicken Sie hierzu mit der rechten Maustaste auf das Projekt, und klicken Sie dann auf **Veröffentlichen**), für dieses Tutorial müssen Sie das aber nicht tun. Sie müssten eine Veröffentlichungskonfiguration einrichten, die den Anwendungs- und Hostingplan verwendet, den Sie beim Konfigurieren des Bots im Azure-Portal verwendet haben.

## <a name="use-the-emulator-to-test-your-bot"></a>Verwenden des Emulators zum Testen des Bots

Sie müssen den [Bot-Emulator](https://github.com/Microsoft/BotFramework-Emulator) installieren, um den Bot lokal zu testen. Sie können den V3- oder V4-Emulator verwenden.

1. Starten Sie Ihren Bot (mit oder ohne Debuggen).
1. Notieren Sie sich die Localhost-Portnummer der Seite. Sie benötigen diese Information für die Interaktion mit Ihrem Bot.
1. Starten Sie den Emulator.
1. Stellen Sie eine Verbindung mit dem Bot her.

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

Wenn ein Benutzer den Bot dazu auffordert, etwas zu tun, wozu der Benutzer angemeldet sein muss, kann der Bot `Microsoft.Bot.Builder.Dialogs.GetTokenDialog` verwenden, um das Abrufen eines Tokens für eine bestimmte Verbindung zu initiieren. Die nachfolgenden Codeausschnitte stammen aus der `GetTokenDialog`-Klasse.

### <a name="check-for-a-cached-token"></a>Prüfen nach einem zwischengespeicherten Token

In diesem Code führt der Bot zunächst eine schnelle Überprüfung durch, um zu ermitteln, ob Azure Bot Service bereits über ein Token für den Benutzer (der vom aktuellen Aktivitätsabsender identifiziert wird) und den jeweiligen Verbindungsnamen (der in der Konfiguration verwendet wird) verfügt. Entweder Azure Bot Service enthält bereits ein zwischengespeichertes Token oder nicht. Der Aufruf von GetUserTokenAsync führt diese „schnelle Überprüfung“ durch. Wenn Azure Bot Service über ein Token verfügt und dieses zurückgibt, kann es sofort verwendet werden. Wenn Azure Bot Service über kein Token verfügt, gibt diese Methode NULL zurück. In diesem Fall kann der Bot eine angepasste OAuth-Karte senden, über die der Benutzer sich anmelden kann.

```csharp
// First ask Bot Service if it already has a token for this user
var token = await context.GetUserTokenAsync(ConnectionName).ConfigureAwait(false);
if (token != null)
{
    // use the token to do exciting things!
}
else
{
    // If Bot Service does not have a token, send an OAuth card to sign in
    await SendOAuthCardAsync(context, (Activity)context.Activity);
}
```

### <a name="send-an-oauthcard-to-the-user"></a>Senden einer OAuth-Karte an den Benutzer

Sie können die OAuth-Karte mit beliebigem Text und Schaltflächentext anpassen. Folgendes ist hier wichtig:

- Legen Sie `ContentType` auf `OAuthCard.ContentType` fest.
- Legen Sie für die `ConnectionName`-Eigenschaft den Namen der Verbindung fest, die Sie verwenden möchten.
- Fügen Sie eine Schaltfläche mit einer Kartenaktion (`CardAction`) vom Typ (`Type`) `ActionTypes.Signin` ein. Beachten Sie, dass Sie keinen Wert für den Anmeldelink angeben müssen.

Am Ende dieses Aufrufs muss der Bot darauf „warten“, dass das Token zurückgegeben wird. Dieser Wartevorgang findet auf dem Hauptdatenstrom von Aktivitäten statt, da der Benutzer möglicherweise viel tun muss, um sich anzumelden.

```csharp
private async Task SendOAuthCardAsync(IDialogContext context, Activity activity)
{
    await context.PostAsync($"To do this, you'll first need to sign in.");

    var reply = await context.Activity.CreateOAuthReplyAsync(_connectionName, _signInMessage, _buttonLabel).ConfigureAwait(false);
    await context.PostAsync(reply);

    context.Wait(WaitForToken);
}
```

### <a name="wait-for-a-tokenresponseevent"></a>Warten auf TokenResponseEvent

In diesem Code wartet die Dialogfeldklasse des Bots auf `TokenResponseEvent` (weitere Informationen zur Weiterleitung an den Dialogstapel finden Sie unten). Die `WaitForToken`-Methode ermittelt zunächst, ob dieses Ereignis gesendet wurde. Wenn es gesendet wurde, kann der Bot es verwenden. Wenn nicht, übergibt die `WaitForToken`-Methode den Text, der an den Bot gesendet wurde, an `GetUserTokenAsync`. Der Grund hierfür besteht darin, dass einige Clients (z.B. WebChat) keinen Code zum Überprüfen von magischem Code benötigen und das Token deshalb direkt in `TokenResponseEvent` senden. Für andere Clients ist der magische Code weiterhin erforderlich (z.B. Facebook oder Slack). Azure Bot Service sendet diesen Clients einen sechsstelligen magischen Code und fordert Benutzer dazu auf, diesen in das Chatfenster einzugeben. Zwar ist dieses Fallback-Verhalten nicht ideal, aber wenn `WaitForToke`n Code empfängt, kann der Bot diesen Code an Azure Bot Service senden und ein Token zurückerhalten. Wenn dieser Aufruf ebenfalls fehlschlägt, können Sie entscheiden, ob Sie einen Fehler melden oder auf andere Weise reagieren möchten. In den meisten Fällen sollte der Bot jedoch nun über ein Benutzertoken verfügen.

Wenn Sie sich die Datei **MessageController.cs** ansehen, werden Sie sehen, dass `Event`-Aktivitäten dieses Typs ebenfalls an den Dialogstapel weitergeleitet werden.

```csharp
private async Task WaitForToken(IDialogContext context, IAwaitable<object> result)
{
    var activity = await result as Activity;

    var tokenResponse = activity.ReadTokenResponseContent();
    if (tokenResponse != null)
    {
        // Use the token to do exciting things!
    }
    else
    {
        if (!string.IsNullOrEmpty(activity.Text))
        {
            tokenResponse = await context.GetUserTokenAsync(ConnectionName,
                                                               activity.Text);
            if (tokenResponse != null)
            {
                // Use the token to do exciting things!
                return;
            }
        }
        await context.PostAsync($"Hmm. Something went wrong. Let's try again.");
        await SendOAuthCardAsync(context, activity);
    }
}
```

### <a name="message-controller"></a>Message-Controller

Beachten Sie, dass das Token von diesem Beispiel-Bot bei aufeinanderfolgenden Aufrufen nie zwischengespeichert werden. Der Grund hierfür ist, dass der Bot das Token immer von Azure Bot Service anfragen kann. Damit wird vermieden, dass der Bot unter anderem den Lebenszyklus des Tokens verwalten und das Token aktualisieren muss, da Azure Bot Service sich darum kümmert.

```csharp
else if(message.Type == ActivityTypes.Event)
{
    if(message.IsTokenResponseEvent())
    {
        await Conversation.SendAsync(message, () => new Dialogs.RootDialog());
    }
}
```
## <a name="additional-resources"></a>Zusätzliche Ressourcen
[Bot Builder SDK](https://github.com/microsoft/botbuilder)

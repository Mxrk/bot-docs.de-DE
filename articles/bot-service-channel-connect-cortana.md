---
title: Verbinden eines Bots mit Cortana | Microsoft-Dokumentation
description: Erfahren Sie, wie ein Bot für den Zugriff über die Cortana-Schnittstelle konfiguriert wird.
keywords: Cortana, Bot-Kanal, Cortana konfigurieren
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 04/30/2018
ms.openlocfilehash: 9e3f2f19c480a9d2fe6df0baea74d449bb584b4f
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49999084"
---
# <a name="connect-a-bot-to-cortana"></a>Verbinden eines Bots mit Cortana

Cortana ist ein sprachaktivierter Kanal, der neben Textkonversation auch Sprachnachrichten senden und empfangen kann. Eine Bot, der mit Cortana verbunden werden soll, muss für Sprache und Text konzipiert sein. Eine Cortana-*Funktion* ist ein Bot, der über einen Cortana-Client aufgerufen werden kann. Der Bot wird nach seiner Veröffentlichung der Liste mit verfügbaren Funktionen hinzugefügt.

Zum Hinzufügen des Cortana-Kanals öffnen Sie den Bot im [Azure-Portal](https://portal.azure.com/), klicken Sie auf das Blatt **Kanäle** und dann auf **Cortana**.

![Hinzufügen des Cortana-Kanals](~/media/channels/cortana-addchannel.png)

## <a name="configure-cortana"></a>Konfigurieren von Cortana

Wenn Sie Ihren Bot mit dem Cortana-Kanal verbinden, ist das Registrierungsformular mit einigen grundlegenden Informationen über Ihren Bot vorausgefüllt. Überprüfen Sie diese Informationen sorgfältig. Dieses Formular umfasst die folgenden Felder.

| Feld | BESCHREIBUNG |
|------|------|
| **Funktionssymbol** | Ein Symbol, das beim Aufruf Ihrer Funktion in der Cortana-Canvas angezeigt wird. Dieses wird auch dort verwendet, wo Funktionen auffindbar sind (z.B. im Microsoft Store). (Max. 32 KB, nur PNG).|
| **Anzeigename** | Der Name Ihrer Cortana-Funktion wird für den Benutzer am oberen Rand der visuellen Benutzeroberfläche angezeigt. (Maximal 30 Zeichen) |
| **Aufrufname** | Diesen Namen sagen Benutzer beim Aufrufen einer Funktion. Er sollte aus höchstens drei Wörtern bestehen und einfach auszusprechen sein. In den [Richtlinien zum Aufrufnamen][invocation] finden Sie weitere Informationen zur Auswahl dieses Namens.|

![Standardeinstellungen](~/media/channels/cortana-defaultsettings.png)

## <a name="general-bot-information"></a>Allgemeine Bot-Informationen

Wählen Sie die Option unter **Manage user identity through connected services section** (Verwalten der Benutzeridentität im Abschnitt „Verbundene Dienste“) aus, um sie zu aktivieren. Füllen Sie das Formular aus.

Mit einem Sternchen (*) gekennzeichnete Felder müssen ausgefüllt werden. Ein Bot muss in Azure veröffentlicht werden, bevor er mit Cortana verbunden werden kann.

![Verwalten der Benutzeridentität – Teil 1](~/media/channels/cortana-manageidentity-1.png)
![Verwalten der Benutzeridentität – Teil 2](~/media/channels/cortana-manageidentity-2.png)

### <a name="when-should-cortana-prompt-for-a-user-to-sign-in"></a>Zeitpunkt der Aufforderung eines Benutzers zum Anmelden durch Cortana

Wählen Sie **Bei Aufruf anmelden**, wenn Cortana den Benutzer anmelden soll, sobald er Ihre Funktion aufruft.

Wählen Sie **Bei Bedarf anmelden**, wenn Sie für die Anmeldung des Benutzers eine Bot Service-Anmeldekarte nutzen. In der Regel verwenden Sie diese Option, wenn der Benutzer nur bei Verwendung eines Features angemeldet werden soll, für das eine Authentifizierung erforderlich ist. Wenn Ihre Funktion eine Nachricht mit der Anmeldekarte als Anlage sendet, ignoriert Cortana die Anmeldekarte und führt den Autorisierungsablauf mit den Einstellungen zum Verbinden des Kontos durch.

### <a name="account-name"></a>Kontoname

Symbol, das angezeigt werden soll, wenn sich der Benutzer bei Ihrer Funktion anmeldet.

### <a name="client-id-for-third-party-services"></a>Client-ID für Dienste von Drittanbietern

Die Anwendungs-ID Ihres Bots. Sie haben die ID bei der Registrierung Ihres Bots erhalten.

### <a name="space-separated-list-of-scopes"></a>Durch Leerzeichen getrennte Liste von Bereichen

Legen Sie die für den Dienst erforderlichen Bereiche fest (siehe Dokumentation des Diensts).

### <a name="authorization-url"></a>URL für Autorisierung

Legen Sie diese Option auf `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` fest.

### <a name="token-options"></a>Tokenoptionen

Wählen Sie **POST**aus.

### <a name="grant-type"></a>Gewährungstyp

Wählen Sie **Autorisierungscode**, um den Codeberechtigungsablauf zu verwenden, oder **Implizit**, um den impliziten Ablauf zu verwenden.

### <a name="token-url"></a>Token-URL

Legen Sie den Gewährungstyp **Autorisierungscode** auf `https://login.microsoftonline.com/common/oauth2/v2.0/token` fest.

### <a name="client-secretpassword-for-third-party-services"></a>Geheimer Clientschlüssel/Kennwort für Dienste von Drittanbietern

Kennwort des Bots. Sie haben das Kennwort bei der Registrierung Ihres Bots erhalten.

### <a name="client-authentication-scheme"></a>Client-Authentifizierungsschema

Wählen Sie die HTTP-Standardauthentifizierung (**HTTP Basic**) aus.

### <a name="internet-access-required-to-authenticate-users"></a>Internet access required to authenticate users (Internetzugriff zum Authentifizieren von Benutzern erforderlich)

Lassen Sie dieses Kontrollkästchen deaktiviert.

### <a name="request-user-profile-data-optional"></a>Anfordern von Benutzerprofildaten (optional)

Cortana bietet Zugriff auf verschiedene Arten von Informationen aus Benutzerprofilen, anhand deren Sie den Bot für den Benutzer anpassen können. Beispiel: Wenn eine Funktion auf den Namen und Standort des Benutzers zugreifen kann, ist eine benutzerdefinierte Antwort wie „Hallo Kamran, ich hoffe, du hast einen angenehmen Tag in Bellevue, Washington, USA.“ möglich.

Klicken Sie auf **Add a user profile request** (Benutzerprofilanforderung hinzufügen), und wählen Sie dann in der Dropdownliste die gewünschten Benutzerprofilinformationen aus. Fügen Sie einen Anzeigenamen hinzu, der für den Zugriff auf diese Informationen über den Code Ihres Bots verwendet werden soll.

### <a name="deploy-on-cortana"></a>Deploy on Cortana (Auf Cortana bereitstellen)

Wenn Sie mit dem Ausfüllen des Registrierungsformulars für Ihre Cortana-Funktion fertig sind, klicken Sie auf **Deploy on Cortana** (Auf Cortana bereitstellen), um die Verbindung abzuschließen. Dadurch gelangen Sie zurück zum Blatt „Kanäle“ Ihres Bots, auf dem Sie sehen sollten, dass er jetzt mit Cortana verbunden ist.

Ihr Bot wurde nun als Cortana-Funktion für Ihr Konto bereitgestellt.

## <a name="next-steps"></a>Nächste Schritte

* [Cortana-Funktionskit](https://aka.ms/CortanaSkillsDocs)
* [Debugging aktivieren](bot-service-debug-cortana-skill.md)
* [Veröffentlichen einer Cortana-Funktion][publish]

[invocation]: https://docs.microsoft.com/en-us/cortana/skills/cortana-invocation-guidelines
[publish]: https://docs.microsoft.com/en-us/cortana/skills/publish-skill
[connected]: https://aka.ms/CortanaSkillsBotConnectedAccount
[CortanaEntity]: https://aka.ms/lgvcto

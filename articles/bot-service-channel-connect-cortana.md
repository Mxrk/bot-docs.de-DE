---
title: Verbinden eines Bots mit Cortana | Microsoft-Dokumentation
description: Erfahren Sie, wie ein Bot für den Zugriff über die Cortana-Schnittstelle konfiguriert wird.
keywords: Cortana, Bot-Kanal, Cortana konfigurieren
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 04/30/2018
ms.openlocfilehash: 6e694ce8b54ebd2405d7496d333c2bb27eb344f1
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301816"
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
| **Beschreibung** | Eine Beschreibung Ihrer Cortana-Funktion. Diese wird auch dort verwendet, wo Funktionen auffindbar sind (z.B. im Microsoft Store). |
| **Kurzbeschreibung** | Eine kurze Beschreibung der Funktionalität Ihrer Funktion, die zur Beschreibung der Funktion im Notizbuch von Cortana verwendet wird. |

## <a name="general-bot-information"></a>Allgemeine Bot-Informationen

Wählen Sie die Option unter **Manage user identity through connected services section** (Verwalten der Benutzeridentität im Abschnitt „Verbundene Dienste“) aus, um sie zu aktivieren. Füllen Sie das Formular aus.

Mit einem Sternchen (*) gekennzeichnete Felder müssen ausgefüllt werden. Bots müssen im Bot-Framework veröffentlicht werden, bevor sie mit Cortana verbunden werden können.

![Allgemeine Informationen angeben](~/media/channels/cortana-details.png)

### <a name="sign-in-at-invocation"></a>Bei Aufruf anmelden

Wählen Sie diese Option aus, wenn Cortana den Benutzer anmelden soll, sobald er Ihre Funktion aufruft.

### <a name="sign-in-when-required"></a>Bei Bedarf anmelden

Wählen Sie diese Option aus, wenn Sie eine SignIn-Karte des Bot-Frameworks zum Anmelden des Benutzers verwenden. In der Regel verwenden Sie diese Option, wenn der Benutzer nur bei Verwendung eines Features angemeldet werden soll, das Authentifizierung erfordert. Wenn Ihre Funktion eine Nachricht mit der SignIn-Karte als Anlage sendet, ignoriert Cortana die SignIn-Karte und führt den Autorisierungsablauf mit den Einstellungen zum Verbinden des Kontos durch.

### <a name="connected-service-icon"></a>Symbol für verbundenen Dienst

Symbol, das angezeigt werden soll, wenn sich der Benutzer bei Ihrer Funktion anmeldet.

### <a name="account-name"></a>Kontoname

Symbol, das angezeigt werden soll, wenn sich der Benutzer bei Ihrer Funktion anmeldet.

### <a name="client-id-for-third-party-services"></a>Client-ID für Dienste von Drittanbietern

Die Anwendungs-ID Ihres Bots. Sie haben die ID bei der Registrierung Ihres Bots erhalten.

### <a name="space-separated-list-of-scopes"></a>Durch Leerzeichen getrennte Liste von Bereichen

Legen Sie die für den Dienst erforderlichen Bereiche fest (siehe Dokumentation des Diensts).

### <a name="authorization-url"></a>URL für Autorisierung

Legen Sie diese Option auf `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` fest.

### <a name="grant-type"></a>Gewährungstyp

Wählen Sie „Autorisierungscode“ aus, um den Codeberechtigungsablauf zu verwenden. Wählen Sie „Implizit“ aus, um den impliziten Ablauf zu verwenden.

### <a name="token-url"></a>Token-URL

Wenn Sie „Autorisierungscode“ auswählen, legen Sie diese auf `https://login.microsoftonline.com/common/oauth2/v2.0/token` fest.

### <a name="client-secretpassword-for-third-party-services"></a>Geheimer Clientschlüssel/Kennwort für Dienste von Drittanbietern

Kennwort des Bots. Sie haben das Kennwort bei der Registrierung Ihres Bots erhalten.

### <a name="client-authentication-scheme"></a>Client-Authentifizierungsschema

Wählen Sie die HTTP-Standardauthentifizierung aus.

### <a name="token-options"></a>Tokenoptionen

Legen Sie POST fest.

### <a name="request-user-profile-data-optional"></a>Anfordern von Benutzerprofildaten (optional)

Cortana bietet Zugriff auf verschiedene Arten von Informationen aus Benutzerprofilen, anhand deren Sie den Bot für den Benutzer anpassen können. Beispiel: Wenn eine Funktion auf den Namen und Standort des Benutzers zugreifen kann, ist eine individuelle Antwort wie „Hallo Kamran, ich hoffe, du hast einen angenehmen Tag in Bellevue, Washington, USA.“

Klicken Sie auf den Link **Add a user profile request** (Benutzerprofilanforderung hinzufügen), und wählen Sie dann in der Dropdownliste die gewünschten Benutzerprofilinformationen aus. Fügen Sie einen Anzeigenamen hinzu, der für den Zugriff auf diese Informationen über den Code Ihres Bots verwendet werden soll.

### <a name="save-skill"></a>Funktion speichern

Wenn Sie mit dem Ausfüllen des Registrierungsformulars für Ihre Cortana-Funktion fertig sind, klicken Sie auf „Speichern“, um die Verbindung abzuschließen. Dadurch gelangen Sie zurück zum Blatt „Kanäle“ Ihres Bots, auf dem Sie sehen sollten, dass er jetzt mit Cortana verbunden ist.

Zu diesem Zeitpunkt wurde Ihr Bot in Ihrem Konto bereits automatisch als Cortana-Funktion bereitgestellt.

## <a name="next-steps"></a>Nächste Schritte

* [Cortana-Funktionskit](https://aka.ms/CortanaSkillsDocs)
* [Debugging aktivieren](bot-service-debug-cortana-skill.md)
* [Veröffentlichen einer Cortana-Funktion][publish]

[invocation]: https://docs.microsoft.com/en-us/cortana/skills/cortana-invocation-guidelines
[publish]: https://docs.microsoft.com/en-us/cortana/skills/publish-skill
[connected]: https://aka.ms/CortanaSkillsBotConnectedAccount
[CortanaEntity]: https://aka.ms/lgvcto

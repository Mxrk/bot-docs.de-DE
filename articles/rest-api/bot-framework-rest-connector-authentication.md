---
title: Authentifizieren von Anforderungen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie API-Anforderungen in der Bot Connector-API und der Bot State-API authentifizieren.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 998586820a0489bc4cca1d25b53cb6ac8162c452
ms.sourcegitcommit: 0b2be801e55f6baa048b49c7211944480e83ba95
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43115045"
---
# <a name="authentication"></a>Authentifizierung

Ihr Bot kommuniziert mit dem Bot Connector-Dienst über HTTP über einen sicheren Kanal (SSL/TLS). Wenn Ihr Bot eine Anforderung an den Connector-Dienst sendet, muss diese Informationen enthalten, anhand derer der Connector-Dienst die Identität bestätigen kann. Ebenso muss der Connector-Dienst, wenn er eine Anforderung an Ihren Bot sendet, Informationen enthalten, anhand der Bot die Identität überprüfen kann. In diesem Artikel werden die Authentifizierungstechnologien und die Anforderungen für Authentifizierung auf Dienstebene beschrieben, die zwischen einem Bot und dem Bot Connector-Dienst erfolgt. Wenn Sie eigenen Authentifizierungscode schreiben, müssen Sie die in diesem Artikel beschriebenen Sicherheitsverfahren implementieren, um Ihrem Bot den Austausch von Nachrichten mit dem Bot Connector-Dienst zu ermöglichen.

> [!IMPORTANT]
> Wenn Sie eigenen Authentifizierungscode schreiben, ist es wichtig, dass alle Sicherheitsverfahren ordnungsgemäß implementiert werden. Durch die Implementierung aller Schritte in diesem Artikel können Sie das Risiko verringern, dass ein Angreifer in der Lage ist, Nachrichten zu lesen, die an Ihren Bot gesendet werden, Nachrichten zu senden, die sich als Ihr Bot ausgeben, und geheime Schlüssel zu stehlen. 

Wenn Sie das [Bot Builder-SDK für .NET](../dotnet/bot-builder-dotnet-overview.md) oder das [Bot Builder-SDK für Node.js](../nodejs/index.md) verwenden, müssen Sie die in diesem Artikel beschriebenen Sicherheitsverfahren nicht implementieren, da das SDK dies automatisch für Sie erledigt. Konfigurieren Sie einfach Ihr Projekt mit der für Ihren Bot während der [Registrierung](../bot-service-quickstart-registration.md) erhaltenen App-ID und dem Kennwort, und das SDK erledigt den Rest.

> [!WARNING]
> Im Dezember 2016 wurde mit Version 3.1 des Bot Framework-Sicherheitsprotokolls Änderungen an mehreren Werten eingeführt, die bei der Tokengenerierung und -validierung verwendet werden. Im Spätherbst 2017 wurde die Version 3.2 des Bot Framework-Sicherheitsprotokolls eingeführt, die Änderungen an Werten enthält, die bei der Tokengenerierung und -validierung verwendet werden.
> Weitere Informationen finden Sie unter [Änderungen am Sicherheitsprotokoll](#security-protocol-changes).

## <a name="authentication-technologies"></a>Authentifizierungstechnologien

Vier Authentifizierungstechnologien werden verwendet, um eine Vertrauensstellung zwischen einem Bot und dem Bot Connector herzustellen:

| Technologie | BESCHREIBUNG |
|----|----|
| **SSL/TLS** | SSL/TLS wird für alle Verbindungen von Dienst zu Dienst verwendet. `X.509v3`-Zertifikate werden verwendet, um die Identität aller HTTPS-Dienste festzustellen. **Clients sollten Dienstzertifikate immer überprüfen, um sicherzustellen, dass sie vertrauenswürdig und gültig sind.** (Clientzertifikate werden NICHT als Teil dieses Schema verwendet.) |
| **OAuth 2.0** | Mit der OAuth 2.0-Anmeldung beim (MSA)/AAD-v2-Anmeldedienst des Microsoft-Kontos wird ein sicheres Token generiert, das ein Bot verwenden kann, um Nachrichten zu senden. Dieses Token ist ein Dienst-zu-Dienst-Token. Es ist keine Benutzeranmeldung erforderlich. |
| **JSON Web Token (JWT)** | JSON Web Token werden verwendet, um Token zu codieren, die vom und an den Bot gesendet werden. **Clients sollten alle empfangenen JWT-Token vollständig gemäß den in diesem Artikel beschriebenen Anforderungen überprüfen**. |
| **OpenID-Metadaten** | Der Bot Connector-Dienst veröffentlicht eine Liste mit gültigen Token, die zum Signieren der eigenen JWT-Token verwendet, in OpenID-Metadaten auf einem bekannten statischen Endpunkt. |

In diesem Artikel wird beschrieben, wie Sie diese Technologien über Standard-HTTPS und -JSON verwenden können. Es werden keine speziellen SDKs benötigt, obwohl Sie vielleicht feststellen, dass Helfer für OpenID etc. nützlich sind.

## <a id="bot-to-connector"></a> Authentifizieren von Anforderungen von Ihrem Bot an den Bot Connector-Dienst

Um mit dem Bot Connector-Dienst zu kommunizieren, müssen Sie ein Zugriffstoken im `Authorization`-Header jeder API-Anforderung unter Verwendung dieses Formats angeben: 

```http
Authorization: Bearer ACCESS_TOKEN
```

Dieses Diagramm zeigt die Schritte für die Authentifizierung von Bot zu Connector:

![Authentifizieren beim MSA-Anmeldedienst und dann beim Bot](../media/connector/auth_bot_to_bot_connector.png)

> [!IMPORTANT]
> Falls nicht bereits geschehen, müssen Sie Ihren [Bot bei Bot Framework registrieren](../bot-service-quickstart-registration.md), um dessen AppID und Kennwort zu erhalten. Sie benötigen die App-ID und das Kennwort des Bots, um ein Zugriffstoken anzufordern.

### <a name="step-1-request-an-access-token-from-the-msaaad-v2-login-service"></a>Schritt 1: Anfordern eines Zugriffstokens vom MSA/AAD-v2-Anmeldedienst

Um ein Zugriffstoken vom MSA/AAD v2-Anmeldedienst anzufordern, führen Sie die folgende Anforderung aus, wobei Sie **MICROSOFT-APP-ID** und **MICROSOFT-APP-PASSWORD** durch die AppID und das Passwort ersetzen, die Sie beim [Registrieren](../bot-service-quickstart-registration.md) Ihres Bots bei Bot Framework erhalten haben.

```http
POST https://login.microsoftonline.com/botframework.com/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=MICROSOFT-APP-ID&client_secret=MICROSOFT-APP-PASSWORD&scope=https%3A%2F%2Fapi.botframework.com%2F.default
```

### <a name="step-2-obtain-the-jwt-token-from-the-msaaad-v2-login-service-response"></a>Schritt 2: Abrufen des JWT-Tokens aus der Antwort des MSA/AAD-v2-Anmeldediensts

Wenn Ihre Anwendung durch den MSA/AAD v2-Anmeldedienst autorisiert wurde, sind im JSON-Antworttext Ihr Zugriffstoken, sein Typ und sein Ablauf (in Sekunden) angegeben. 

Wenn Sie das Token zum `Authorization`-Header einer Anforderung hinzufügen, müssen Sie genau den Wert verwenden, der in dieser Antwort angegeben ist (d. h. der Tokenwert darf nicht mit Escapezeichen versehen oder kodiert werden). Das Zugriffstoken ist bis zu seinem Ablauf gültig. Um zu verhindern, dass das Ablaufen von Token die Leistung Ihres Bots beeinträchtigt, können Sie das Token zwischenspeichern und proaktiv aktualisieren.

Im folgenden Beispiel wird eine Antwort vom MSA/AAD-v2-Anmeldedienst gezeigt:

```http
HTTP/1.1 200 OK
... (other headers) 
```

```json
{
    "token_type":"Bearer",
    "expires_in":3600,
    "ext_expires_in":3600,
    "access_token":"eyJhbGciOiJIUzI1Ni..."
}
```

### <a name="step-3-specify-the-jwt-token-in-the-authorization-header-of-requests"></a>Schritt 3: Angeben des JWT-Tokens im Autorisierungsheader von Anforderungen

Wenn Sie eine API-Anforderung an den Bot Connector-Dienst senden, geben Sie das Zugriffstoken im `Authorization`-Header der Anforderung unter Verwendung dieses Formats an:

```http
Authorization: Bearer ACCESS_TOKEN
```

Alle Anforderungen, die Sie an den Bot Connector-Dienst senden, müssen das Zugriffstoken im `Authorization`-Header enthalten. Wenn das Token ist richtig formatiert ist, nicht abgelaufen ist und durch den MSA/AAD-v2-Anmeldedienst generiert wurde, autorisiert der Bot Connector-Dienst die Anforderung. Zusätzliche Überprüfungen werden ausgeführt, um sicherzustellen, dass das Token zum Bot gehört, der die Anforderung gesendet hat.

Im folgenden Beispiel wird gezeigt, wie das Zugriffstoken im `Authorization`-Header der Anforderung angegeben wird. 

```http
POST https://smba.trafficmanager.net/apis/v3/conversations/12345/activities 
Authorization: Bearer eyJhbGciOiJIUzI1Ni...
    
(JSON-serialized Activity message goes here)
```

> [!IMPORTANT]
> Geben Sie das JWT-Token nur im `Authorization`-Header von Anforderungen an, die Sie an den Bot Connector-Dienst senden. Senden Sie das Token NICHT über unsichere Kanäle, und fügen Sie es NICHT in HTTP-Anforderungen ein, die Sie an andere Dienste senden. Das JWT-Token, das Sie vom MSA/AAD-v2-Anmeldedienst erhalten, gleicht einem Kennwort und sollte mit äußerster Sorgfalt behandelt werden. Jeder, der in den Besitz des Tokens kommt, kann es zum Ausführen von Vorgängen im Namen Ihres Bots verwenden. 

#### <a name="bot-to-connector-example-jwt-components"></a>Bot an Connector: Beispiel für JWT-Komponenten

```json
header:
{
  typ: "JWT",
  alg: "RS256",
  x5t: "<SIGNING KEY ID>",
  kid: "<SIGNING KEY ID>"
},
payload:
{
  aud: "https://api.botframework.com",
  iss: "https://sts.windows.net/d6d49420-f39b-4df7-a1dc-d59a935871db/",
  nbf: 1481049243,
  exp: 1481053143,
  appid: "<YOUR MICROSOFT APP ID>",
  ... other fields follow
}
```

> [!NOTE]
> Die tatsächlichen Felder können in der Praxis variieren. Erstellen und überprüfen Sie alle JWT-Token wie oben angegeben.

## <a id="connector-to-bot"></a> Authentifizieren von Anforderungen vom Bot Connector-Dienst an Ihren Bot

Wenn der Bot Connector-Dienst eine Anforderung an Ihren Bot sendet, wird ein signiertes JWT-Token im `Authorization`-Header der Anforderung angegeben. Ihr Bot kann Aufrufe des Bot Connector-Diensts authentifizieren, indem er die Echtheit des signierten JWT-Tokens überprüft. 

Dieses Diagramm zeigt die Schritte für die Authentifizierung von Connector zu Bot:

![Authentifizieren von Aufrufen von Bot Connector zu Ihrem Bot](../media/connector/auth_bot_connector_to_bot.png)

### <a id="openid-metadata-document"></a> Schritt 2: Abrufen des OpenID-Metadatendokuments

Das OpenID-Metadatendokument gibt den Speicherort eines zweiten Dokuments an, in dem die gültigen Signaturschlüssel des Bot Connector-Diensts auflistet sind. Um das OpenID-Metadatendokument zu erhalten, geben Sie diese Anforderung über HTTPS aus:

```http
GET https://login.botframework.com/v1/.well-known/openidconfiguration
```

> [!TIP]
> Dies ist eine statische URL, die Sie in Ihrer Anwendung hartcodieren können. 

Im folgenden Beispiel wird ein OpenID-Metadatendokument gezeigt, das als Antwort auf die `GET`-Anforderung zurückgegeben wird. Die `jwks_uri`-Eigenschaft gibt den Speicherort des Dokuments an, das die gültigen Signaturschlüssel des Bot Connector-Diensts enthält.

```json
{
    "issuer": "https://api.botframework.com",
    "authorization_endpoint": "https://invalid.botframework.com",
    "jwks_uri": "https://login.botframework.com/v1/.well-known/keys",
    "id_token_signing_alg_values_supported": [
      "RS256"
    ],
    "token_endpoint_auth_methods_supported": [
      "private_key_jwt"
    ]
}
```

### <a id="connector-to-bot-step-3"></a> Schritt 3: Abrufen der Liste der gültigen Signaturschlüssel

Um die Liste der gültigen Signaturschlüssel zu erhalten, geben Sie eine `GET`-Anforderung über HTTPS an die von der `jwks_uri`-Eigenschaft im OpenID-Metadatendokument angegebene URL aus. Beispiel: 

```http
GET https://login.botframework.com/v1/.well-known/keys
```

Der Antworttext gibt das Dokument im [JWK-Format](https://tools.ietf.org/html/rfc7517) an, enthält jedoch auch eine zusätzliche Eigenschaft für jeden Schlüssel: `endorsements`. Die Liste der Schlüssel ist relativ stabil und kann über längere Zeiträume (standardmäßig 5 Tage innerhalb des Bot Builder-SDKs) zwischengespeichert werden.

Die `endorsements`-Eigenschaft in jedem Schlüssel enthält eine oder mehrere Endorsement-Zeichenfolgen, die Sie verwenden können, um sicherzustellen, dass die in der `channelId`-Eigenschaft im [Activity][Activity]-Objekt angegebene Kanal-ID der eingehenden Anforderung echt ist. Die Liste der Kanal-IDs, die Endorsements erfordern, kann in jedem Bot konfiguriert werden. Standardmäßig ist es die Liste aller veröffentlichten Kanal-IDs, aber Botentwickler können bestimmte Kanal-ID-Werde in beiden Fällen überschreiben. Wenn das Endorsement für eine Kanal-ID erforderlich ist:

- Sie sollten verlangen, dass jedes mit dieser Kanal-ID an Ihren Bot gesendete [Activity][Activity]-Objekt zudem über ein JWT-Token verfügt, das mit einem Endorsement für diesen Kanal signiert ist. 
- Wenn kein Endorsement vorhanden ist, sollte Ihr Bot die Anforderung unter Rückgabe eines **HTTP 403 (Unzulässig)**-Statuscodes abweisen.

### <a name="step-4-verify-the-jwt-token"></a>Schritt 4: Überprüfen des JWT-Tokens

Um die Echtheit des Tokens, das vom Bot Connector-Dienst gesendet wurde, zu überprüfen, müssen Sie das Token aus dem `Authorization`-Header der Anforderung extrahieren, das Token analysieren, seinen Inhalt überprüfen und seine Signatur überprüfen. 

JWT-Analysebibliotheken sind für viele Plattformen verfügbar, und die meisten implementieren sichere und zuverlässige Analyse für JWT-Token. Allerdings müssen Sie diese Bibliotheken typischerweise so konfigurieren, dass bestimmte Eigenschaften des Tokens (Herausgeber, Zielgruppe usw.) korrekte Werte enthalten. Wenn Sie Token analysieren möchten, müssen Sie die Analysebibliothek konfigurieren oder eine eigene Überprüfung schreiben, um sicherzustellen, dass das Token diese Anforderungen erfüllt:

1. Das Token wurde im HTTP `Authorization`-Header mit dem „Bearer“-Schema gesendet.
2. Das Token liegt in gültigem, mit dem [JWT-Standard](http://openid.net/specs/draft-jones-json-web-token-07.html) konformen JSON-Format vor.
3. Das Token enthält einen „issuer“-Anspruch mit dem `https://api.botframework.com`-Wert.
4. Das Token enthält einen „audience“-Anspruchs mit einem Wert, der der Microsoft App-ID des Bots entspricht.
5. Die Gültigkeit des Tokens ist nicht abgelaufen. Branchenstandard-Uhrabweichung beträgt 5 Minuten.
6. Das Token verfügt über eine gültige kryptographische Signatur mit einem Schlüssel, der im OpenID-Schlüsseldokument aufgeführt ist, das in [Schritt 3](#connector-to-bot-step-3) abgerufen wurde, und verwendet einen Signaturalgorithmus, der in der `id_token_signing_alg_values_supported`-Eigenschaft des in [Schritt 2](#openid-metadata-document) abgerufenen Open ID-Metadatendokuments angegeben ist.
7. Das Token enthält einen „serviceUrl“-Anspruch mit einem Wert, der der `servieUrl`-Eigenschaft im Stamm des [Activity][Activity]-Objekts der eingehenden Anforderung entspricht. 

Wenn das Token nicht alle diese Anforderungen erfüllt, sollte Ihr Bot die Anforderung unter Rückgabe eines **HTTP 403 (Unzulässig)**-Statuscodes abweisen.

> [!IMPORTANT]
> Alle diese Anforderungen sind wichtig, besonders die Anforderungen 4 und 6. Wenn nicht ALLE diese Verifizierungsanforderungen implementiert werden, ist der Bot offen für Angriffe, die dazu führen könnten, dass der Bot sein JWT-Token preisgibt.

Implementierer sollten keine Möglichkeit bieten, die Validierung des an den Bot gesendeten JWT-Tokens zu deaktivieren.

#### <a name="connector-to-bot-example-jwt-components"></a>Connector an Bot: Beispiel für JWT-Komponenten

```json
header:
{
  typ: "JWT",
  alg: "RS256",
  x5t: "<SIGNING KEY ID>",
  kid: "<SIGNING KEY ID>"
},
payload:
{
  aud: "<YOU MICROSOFT APP ID>",
  iss: "https://api.botframework.com",
  nbf: 1481049243,
  exp: 1481053143,
  ... other fields follow
}
```

> [!NOTE]
> Die tatsächlichen Felder können in der Praxis variieren. Erstellen und überprüfen Sie alle JWT-Token wie oben angegeben.

## <a id="emulator-to-bot"></a> Authentifizieren von Anforderungen vom Bot Framework Emulator an Ihren Bot

> [!WARNING]
> Im Spätherbst 2017 wurde Version 3.2. des Bot Framework -Sicherheitsprotokolls eingeführt. Diese neue Version enthält einen neuen „issuer“-Wert in Token, die zwischen dem Bot Framework Emulator und Ihrem Bot ausgetauscht werden. Zur Vorbereitung dieser Änderung beschreiben die unten aufgeführten Schritte, wie Sie „issuer“-Werte sowohl für Version 3.1 als auch Version 3.2 überprüfen. 

Der [Bot Framework Emulator](../bot-service-debug-emulator.md) ist ein Desktoptool, das Sie zum Testen der Funktionalität Ihres Bots verwenden können. Der Bot Framework Emulator verwendet zwar die gleichen [Authentifizierungstechnologien](#authentication-technologies) wie oben beschrieben, ist aber nicht in der Lage, den echten Bot Connector-Dienst nachzuahmen. Stattdessen verwendet er die Microsoft App-ID und das Microsoft App-Kennwort, die Sie angeben, wenn Sie den Emulator mit Ihrem Bot verbinden, um Token zu erstellen, die mit denen identisch sind, die der Bot erstellt. Wenn der Emulator eine Anforderung an Ihren Bot sendet, wird das JWT-Token im `Authorization`-Header der Anforderung angegeben – im Wesentlichen mit den eigenen Anmeldeinformationen des Bots zum Authentifizieren der Anforderung. 

Wenn Sie eine Authentifizierungsbibliothek implementieren und Anforderungen vom Bot Framework-Emulator akzeptiert werden sollen, müssen Sie diesen zusätzlichen Überprüfungspfad hinzufügen. Der Pfad gleicht in der Struktur dem [Connector -> Bot](#connector-to-bot)-Überprüfungspfad, verwendet jedoch das OpenID-Dokument des MSA anstelle des Bot Connector-OpenID-Dokuments.

Dieses Diagramm zeigt die Schritte für die Authentifizierung von Emulator zu Bot:

![Authentifizieren von Aufrufen vom Bot Framework Emulator an Ihren Bot](../media/connector/auth_bot_framework_emulator_to_bot.png)

---
### <a name="step-2-get-the-msa-openid-metadata-document"></a>Schritt 2: Abrufen des MSA OpenID-Metadatendokuments

Das OpenID-Metadatendokument gibt den Speicherort eines zweiten Dokuments an, in dem die gültigen Signaturschlüssel auflistet sind. Um das MSA OpenID-Metadatendokument zu erhalten, geben Sie diese Anforderung über HTTPS aus:

```http
GET https://login.microsoftonline.com/botframework.com/v2.0/.well-known/openid-configuration
```

Im folgenden Beispiel wird ein OpenID-Metadatendokument gezeigt, das als Antwort auf die `GET`-Anforderung zurückgegeben wird. Die `jwks_uri`-Eigenschaft gibt den Speicherort des Dokuments an, das die gültigen Signaturschlüssel enthält.

```json
{
    "authorization_endpoint":"https://login.microsoftonline.com/common/oauth2/v2.0/authorize",
    "token_endpoint":"https://login.microsoftonline.com/common/oauth2/v2.0/token",
    "token_endpoint_auth_methods_supported":["client_secret_post","private_key_jwt"],
    "jwks_uri":"https://login.microsoftonline.com/common/discovery/v2.0/keys",
    ...
}
```

### <a id="emulator-to-bot-step-3"></a> Schritt 3: Abrufen der Liste der gültigen Signaturschlüssel

Um die Liste der gültigen Signaturschlüssel zu erhalten, geben Sie eine `GET`-Anforderung über HTTPS an die von der `jwks_uri`-Eigenschaft im OpenID-Metadatendokument angegebene URL aus. Beispiel: 

```http
GET https://login.microsoftonline.com/common/discovery/v2.0/keys 
Host: login.microsoftonline.com
```

Der Antworttext gibt das Dokument im [JWK-Format](https://tools.ietf.org/html/rfc7517) an. 

### <a name="step-4-verify-the-jwt-token"></a>Schritt 4: Überprüfen des JWT-Tokens

Um die Echtheit des Tokens, das vom Emulator gesendet wurde, zu überprüfen, müssen Sie das Token aus dem `Authorization`-Header der Anforderung extrahieren, das Token analysieren, seinen Inhalt überprüfen und seine Signatur überprüfen. 

JWT-Analysebibliotheken sind für viele Plattformen verfügbar, und die meisten implementieren sichere und zuverlässige Analyse für JWT-Token. Allerdings müssen Sie diese Bibliotheken typischerweise so konfigurieren, dass bestimmte Eigenschaften des Tokens (Herausgeber, Zielgruppe usw.) korrekte Werte enthalten. Wenn Sie Token analysieren möchten, müssen Sie die Analysebibliothek konfigurieren oder eine eigene Überprüfung schreiben, um sicherzustellen, dass das Token diese Anforderungen erfüllt:

1. Das Token wurde im HTTP `Authorization`-Header mit dem „Bearer“-Schema gesendet.
2. Das Token liegt in gültigem, mit dem [JWT-Standard](http://openid.net/specs/draft-jones-json-web-token-07.html) konformen JSON-Format vor.
3. Das Token enthält einen „issuer“-Anspruch mit dem `https://sts.windows.net/d6d49420-f39b-4df7-a1dc-d59a935871db/`- oder `https://sts.windows.net/f8cdef31-a31e-4b4a-93e4-5f571e91255a/`-Wert. (Durch Überprüfen auf beide „issuer“-Werte wird sichergestellt, dass Sie die „issuer“-Werte sowohl für das Sicherheitsprotokoll der Version 3.1 als auch der Version 3.2 prüfen)
4. Das Token enthält einen „audience“-Anspruchs mit einem Wert, der der Microsoft App-ID des Bots entspricht.
5. Das Token enthält einen „appid“-Anspruchs mit dem Wert, der der Microsoft App-ID des Bots entspricht.
6. Die Gültigkeit des Tokens ist nicht abgelaufen. Branchenstandard-Uhrabweichung beträgt 5 Minuten.
7. Das Token verfügt über eine gültige kryptografische Signatur mit einem Schlüssel, der in Dokument der OpenID-Schlüssel aufgeführt ist, das in [Schritt 3](#emulator-to-bot-step-3) abgerufen wurde.

> [!NOTE]
> Anforderung 5 gilt speziell für den Emulator-Überprüfungspfad. 

Wenn das Token nicht alle diese Anforderungen erfüllt, sollte Ihr Bot die Anforderung unter Rückgabe eines **HTTP 403 (Unzulässig)**-Statuscodes beenden.

> [!IMPORTANT]
> Alle diese Anforderungen sind wichtig, besonders die Anforderungen 4 und 7. Wenn nicht ALLE diese Verifizierungsanforderungen implementiert werden, ist der Bot offen für Angriffe, die dazu führen könnten, dass der Bot sein JWT-Token preisgibt.

#### <a name="emulator-to-bot-example-jwt-components"></a>Emulator an Bot: Beispiel für JWT-Komponenten

```json
header:
{
  typ: "JWT",
  alg: "RS256",
  x5t: "<SIGNING KEY ID>",
  kid: "<SIGNING KEY ID>"
},
payload:
{
  aud: "<YOUR MICROSOFT APP ID>",
  iss: "https://sts.windows.net/d6d49420-f39b-4df7-a1dc-d59a935871db/",
  nbf: 1481049243,
  exp: 1481053143,
  ... other fields follow
}
```

> [!NOTE]
> Die tatsächlichen Felder können in der Praxis variieren. Erstellen und überprüfen Sie alle JWT-Token wie oben angegeben.

## <a name="security-protocol-changes"></a>Änderungen des Sicherheitsprotokolls

> [!WARNING]
> Die Unterstützung für Version 3.0 des Sicherheitsprotokolls wurde zum **31. Juli 2017** eingestellt. Wenn Sie Ihren eigenen Authentifizierungscode geschrieben haben (d. h. den Bot nicht mit dem Bot Builder-SDK erstellt haben), müssen Sie ein Upgrade auf Version 3.1 des Sicherheitsprotokolls vornehmen, indem Sie Ihre Anwendung zur Verwendung der unten aufgeführten v3.1-Werte aktualisieren. 

### <a name="bot-to-connector-authenticationbot-to-connector"></a>[Bot zu Connector-Authentifizierung](#bot-to-connector)

#### <a name="oauth-login-url"></a>OAuth-Anmelde-URL

| Protokollversion | Gültiger Wert |
|----|----|
| v3.1 und v3.2 | `https://login.microsoftonline.com/botframework.com/oauth2/v2.0/token` |

#### <a name="oauth-scope"></a>OAuth-Bereich

| Protokollversion | Gültiger Wert |
|----|----|
| v3.1 und v3.2 |  `https://api.botframework.com/.default` |

### <a name="connector-to-bot-authenticationconnector-to-bot"></a>[Connector zu Bot Authentifizierung](#connector-to-bot)

#### <a name="openid-metadata-document"></a>OpenID-Metadatendokument

| Protokollversion | Gültiger Wert |
|----|----|
| v3.1 und v3.2 | `https://login.botframework.com/v1/.well-known/openidconfiguration` |

#### <a name="jwt-issuer"></a>JWT-Aussteller

| Protokollversion | Gültiger Wert |
|----|----|
| v3.1 und v3.2 | `https://api.botframework.com` |

### <a name="emulator-to-bot-authenticationemulator-to-bot"></a>[Emulator zu Bot Authentifizierung](#emulator-to-bot)

#### <a name="oauth-login-url"></a>OAuth-Anmelde-URL

| Protokollversion | Gültiger Wert |
|----|----|
| v3.1 und v3.2 | `https://login.microsoftonline.com/botframework.com/oauth2/v2.0/token` |

#### <a name="oauth-scope"></a>OAuth-Bereich

| Protokollversion | Gültiger Wert |
|----|----|
| v3.1 und v3.2 |  Microsoft App-ID Ihres Bots und `/.default` |

#### <a name="jwt-audience"></a>JWT-Zielgruppe

| Protokollversion | Gültiger Wert |
|----|----|
| v3.1 und v3.2 | Microsoft App-ID Ihres Bots |

#### <a name="jwt-issuer"></a>JWT-Aussteller

| Protokollversion | Gültiger Wert |
|----|----|
| v3.1 | `https://sts.windows.net/d6d49420-f39b-4df7-a1dc-d59a935871db/` |
| v3.2 | `https://sts.windows.net/f8cdef31-a31e-4b4a-93e4-5f571e91255a/` |

#### <a name="openid-metadata-document"></a>OpenID-Metadatendokument

| Protokollversion | Gültiger Wert |
|----|----|
| v3.1 und v3.2 | `https://login.microsoftonline.com/botframework.com/v2.0/.well-known/openid-configuration` |

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Problembehandlung bei der Bot Framework-Authentifizierung](../bot-service-troubleshoot-authentication-problems.md)
- [JSON Web Token (JWT) draft-jones-json-web-token-07](http://openid.net/specs/draft-jones-json-web-token-07.html)
- [JSON Web Signature (JWS) draft-jones-json-web-signature-04](https://tools.ietf.org/html/draft-jones-json-web-signature-04)
- [JSON Web Key (JWK) RFC 7517](https://tools.ietf.org/html/rfc7517)

[Activity]: bot-framework-rest-connector-api-reference.md#activity-object

---
title: Authentifizierung | Microsoft-Dokumentation
description: Informationen zum Authentifizieren der API-Anforderungen in Direct Line API v3. 0.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 18a5815a96e2052a54c48f6af211d8b28e20d983
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39302200"
---
# <a name="authentication"></a>Authentifizierung

Ein Client kann Anforderungen an Direct Line API 3.0 entweder über ein **Geheimnis** authentifizieren, das Sie von der [Konfigurationsseite des Direct Line-Kanals im Bot-Frameworkportal abrufen](../bot-service-channel-connect-directline.md), oder mithilfe eines **Tokens**, das Sie zur Laufzeit abrufen. Das Geheimnis bzw. Token muss im `Authorization`-Header jeder Anforderung mit diesem Format angegeben werden: 

```http
Authorization: Bearer SECRET_OR_TOKEN
```

## <a name="secrets-and-tokens"></a>Geheimnisse und Token

Ein Direct Line-**Geheimnis** ist ein Hauptschlüssel, der verwendet werden kann, um auf eine beliebige Konversation zuzugreifen, die zu dem zugehörigen Bot gehört. Ein **Geheimnis** kann auch zum Abrufen eines **Tokens** verwendet werden. Geheimnisse laufen nicht ab. 

Ein Direct Line-**Token** ist ein Schlüssel, der zum Zugriff auf eine einzelne Konversation verwendet werden kann. Ein Token läuft zwar ab, kann aber aktualisiert werden. 

Wenn Sie eine Dienst-zu-Dienst-Anwendung erstellen, kann die Angabe des **Geheimnisses** im `Authorization`-Header der Direct Line-API-Anforderungen der einfachste Ansatz sein. Wenn Sie eine Anwendung schreiben, in der der Client in einem Webbrowser oder einer mobilen App ausgeführt wird, sollten Sie Ihr Geheimnis gegen ein Token (das nur für eine einzelne Konversation funktioniert und abläuft, wenn es nicht aktualisiert wird) austauschen und das **Token** im `Authorization`-Header der Direct Line API-Anforderungen angeben. Wählen Sie das Sicherheitsmodell aus, das für Sie am besten geeignet ist.

> [!NOTE]
> Ihre Direct Line-Clientanmeldeinformationen unterscheiden sich von den Anmeldeinformationen Ihres Bots. Dies ermöglicht Ihnen, Ihre Schlüssel unabhängig zu überarbeiten und Clienttoken gemeinsam zu nutzen, ohne das Kennwort Ihres Bots offenzulegen. 

## <a name="get-a-direct-line-secret"></a>Abrufen eines Direct Line-Geheimnisses

Sie können über die Direct Line-Kanalkonfigurationsseite im <a href="https://dev.botframework.com/" target="_blank">Bot-Frameworkportal</a> für Ihren Bot [ein Direct Line-Geheimnis abrufen](../bot-service-channel-connect-directline.md):

![Direct Line-Konfiguration](../media/direct-line-configure.png)

## <a id="generate-token"></a> Generieren eines Direct Line-Tokens

Um ein Direct Line-Token zu generieren, das zum Zugriff auf eine einzelne Konversation verwendet werden kann, rufen Sie zunächst das Direct Line-Geheimnis von der Direct Line-Kanalkonfigurationsseite im <a href="https://dev.botframework.com/" target="_blank">Bot-Frameworkportal</a> ab. Geben Sie dann diese Anforderung zum Austausch Ihres Direct Line-Geheimnisses gegen ein Direct Line-Token aus:

```http
POST https://directline.botframework.com/v3/directline/tokens/generate
Authorization: Bearer SECRET
```

Ersetzen Sie im `Authorization`-Header dieser Anforderung **SECRET** mit dem Wert Ihres Direct Line-Geheimnisses.

Die folgenden Codeausschnitte enthalten ein Beispiel der Anforderung „Token generieren“ und der Antwort.

### <a name="request"></a>Anforderung

```http
POST https://directline.botframework.com/v3/directline/tokens/generate
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0
```

### <a name="response"></a>response

Wenn die Anforderung erfolgreich war, enthält die Antwort ein `token`, das für eine Unterhaltung gültig ist, und einen `expires_in`-Wert, der die Anzahl von Sekunden bis zum Ablauf des Tokens angibt. Damit das Token nutzbar bleibt, müssen Sie [das Token aktualisieren](#refresh-token), bevor es abläuft.

```http
HTTP/1.1 200 OK
[other headers]
```

```json
{
  "conversationId": "abc123",
  "token": "RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn",
  "expires_in": 1800
}
```

### <a name="generate-token-versus-start-conversation"></a>„Token generieren“ im Vergleich zu „Unterhaltung starten“

Der Vorgang „Token generieren“ (`POST /v3/directline/tokens/generate`) ist vergleichbar mit dem Vorgang [Unterhaltung starten](bot-framework-rest-direct-line-3-0-start-conversation.md) (`POST /v3/directline/conversations`). Beide Vorgänge geben ein `token` zurück, mit dem auf eine einzelne Unterhaltung zugegriffen werden kann. Jedoch im Gegensatz zu dem Vorgang „Unterhaltung starten“ startet der Vorgang „Token generieren“ keine Unterhaltung, stellt keinen Kontakt mit dem Bot her und erstellt keine Streaming-WebSocket-URL. 

Wenn Sie beabsichtigen, das Token an Clients zu verteilen, und möchten, dass diese die Unterhaltung initiieren, verwenden Sie den Vorgang „Token generieren“. Wenn Sie die Unterhaltung sofort beginnen möchten, verwenden Sie stattdessen den Vorgang [Unterhaltung starten](bot-framework-rest-direct-line-3-0-start-conversation.md).

## <a id="refresh-token"></a> Aktualisieren eines Direct Line-Tokens

Ein Direct Line-Token kann beliebig oft aktualisiert werden, solange es nicht abgelaufen ist. Ein abgelaufenes Token kann nicht aktualisiert werden. Um ein Direct Line-Token zu aktualisieren, geben Sie diese Anforderung aus: 

```http
POST https://directline.botframework.com/v3/directline/tokens/refresh
Authorization: Bearer TOKEN_TO_BE_REFRESHED
```

Ersetzen Sie im `Authorization`-Header dieser Anforderung **TOKEN_TO_BE_REFRESHED** mit dem Direct Line-Token, das Sie aktualisieren möchten.

Die folgenden Codeausschnitte enthalten ein Beispiel der Anforderung „Token aktualisieren“ und der Antwort.

### <a name="request"></a>Anforderung

```http
POST https://directline.botframework.com/v3/directline/tokens/refresh
Authorization: Bearer CurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn
```

### <a name="response"></a>response

Wenn die Anforderung erfolgreich war, enthält die Antwort ein neues `token`, das für dieselbe Unterhaltung wie das vorherige Token gültig ist, und einen `expires_in`-Wert, der die Anzahl von Sekunden bis zum Ablauf des neuen Tokens angibt. Damit das neue Token nutzbar bleibt, müssen Sie [das Token aktualisieren](#refresh-token), bevor es abläuft.

```http
HTTP/1.1 200 OK
[other headers]
```

```json
{
  "conversationId": "abc123",
  "token": "RCurR_XV9ZA.cwA.BKA.y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xniaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0",
  "expires_in": 1800
}
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Wichtige Begriffe](bot-framework-rest-direct-line-3-0-concepts.md)
- [Herstellen der Verbindung eines Bots mit Direct Line](../bot-service-channel-connect-directline.md)
---
title: Authentifizierung | Microsoft-Dokumentation
description: Informationen zum Authentifizieren der API-Anforderungen in Direct Line API v1. 1.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 6e83cc45e925f5d94b70260de6ac54e6f4052ca4
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301904"
---
# <a name="authentication"></a>Authentifizierung

> [!IMPORTANT]
> Dieser Artikel beschreibt die Authentifizierung in Direct Line API v1. 1. Wenn Sie eine neue Verbindung zwischen Ihrer Clientanwendung und dem Bot herstellen, verwenden Sie stattdessen [Direct Line API 3.0](bot-framework-rest-direct-line-3-0-authentication.md).

Ein Client kann Anforderungen an Direct Line API 1.1 entweder über ein **Geheimnis** authentifizieren, das Sie von der [Konfigurationsseite des Direct Line-Kanals im Bot-Frameworkportal abrufen](../bot-service-channel-connect-directline.md), oder mithilfe eines **Tokens**, das Sie zur Laufzeit abrufen.

Das Geheimnis bzw. Token muss im `Authorization`-Header jeder Anforderung angegeben werden, entweder mit dem Schema „Bearer“ oder dem Schema „BotConnector“. 

**Bearer-Schema**:
```http
Authorization: Bearer SECRET_OR_TOKEN
```

**BotConnector-Schema**:
```http
Authorization: BotConnector SECRET_OR_TOKEN
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
POST https://directline.botframework.com/api/tokens/conversation
Authorization: Bearer SECRET
```

Ersetzen Sie im `Authorization`-Header dieser Anforderung **SECRET** mit dem Wert Ihres Direct Line-Geheimnisses.

Die folgenden Codeausschnitte enthalten ein Beispiel der Anforderung „Token generieren“ und der Antwort.

### <a name="request"></a>Anforderung

```http
POST https://directline.botframework.com/api/tokens/conversation
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0
```

### <a name="response"></a>response

Wenn die Anforderung erfolgreich ist, enthält die Antwort ein Token, das für eine Konversation gültig ist. Das Token läuft in 30 Minuten ab. Damit das Token nutzbar bleibt, müssen Sie [das Token aktualisieren](#refresh-token), bevor es abläuft.

```http
HTTP/1.1 200 OK
[other headers]

"RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn"
```

### <a name="generate-token-versus-start-conversation"></a>„Token generieren“ im Vergleich zu „Unterhaltung starten“

Der Vorgang „Token generieren“ (`POST /api/tokens/conversation`) ist vergleichbar mit dem Vorgang [Unterhaltung starten](bot-framework-rest-direct-line-1-1-start-conversation.md) (`POST /api/conversations`). Beide Vorgänge geben ein `token` zurück, mit dem auf eine einzelne Unterhaltung zugegriffen werden kann. Jedoch im Gegensatz zu dem Vorgang „Unterhaltung starten“ startet der Vorgang „Token generieren“ weder eine Unterhaltung, noch stellt er einen Kontakt mit dem Bot her. 

Wenn Sie beabsichtigen, das Token an Clients zu verteilen, und möchten, dass diese die Unterhaltung initiieren, verwenden Sie den Vorgang „Token generieren“. Wenn Sie die Unterhaltung sofort beginnen möchten, verwenden Sie stattdessen den Vorgang [Unterhaltung starten](bot-framework-rest-direct-line-1-1-start-conversation.md).

## <a id="refresh-token"></a> Aktualisieren eines Direct Line-Tokens

Ein Direct Line-Token ist ab dem Zeitpunkt, zu dem es generiert wurde, 30 Minuten gültig und kann beliebig oft aktualisiert werden, solange es nicht abgelaufen ist. Ein abgelaufenes Token kann nicht aktualisiert werden. Um ein Direct Line-Token zu aktualisieren, geben Sie diese Anforderung aus:

```http
POST https://directline.botframework.com/api/tokens/{conversationId}/renew
Authorization: Bearer TOKEN_TO_BE_REFRESHED
```

Ersetzen Sie im URI der Anforderung **{conversationId}** mit der ID der Konversation, für die das Token gültig ist, und im `Authorization`-Header dieser Anforderung **TOKEN_TO_BE_REFRESHED** mit dem Direct Line-Token, das Sie aktualisieren möchten.

Die folgenden Codeausschnitte enthalten ein Beispiel der Anforderung „Token aktualisieren“ und der Antwort.

### <a name="request"></a>Anforderung

```http
POST https://directline.botframework.com/api/tokens/abc123/renew
Authorization: Bearer CurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn
```

### <a name="response"></a>response

Wenn die Anforderung erfolgreich ist, enthält die Antwort ein neues Token, das für dieselbe Konversation wie das vorherige gültig ist. Das neue Token läuft in 30 Minuten ab. Damit das neue Token nutzbar bleibt, müssen Sie [das Token aktualisieren](#refresh-token), bevor es abläuft.

```http
HTTP/1.1 200 OK
[other headers]

"RCurR_XV9ZA.cwA.BKA.y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xniaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0"
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Wichtige Begriffe](bot-framework-rest-direct-line-1-1-concepts.md)
- [Herstellen der Verbindung eines Bots mit Direct Line](../bot-service-channel-connect-directline.md)
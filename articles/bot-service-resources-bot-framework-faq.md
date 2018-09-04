---
title: Häufig gestellte Fragen zu Bot Service | Microsoft-Dokumentation
description: Hier finden Sie eine Liste mit häufig gestellten Fragen zu Elementen von Bot Framework und dazu, wann neue Features verfügbar werden.
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 08/28/2018
ms.openlocfilehash: 63aa65e2591d9f98d763863d8d4d56cd0df185ea
ms.sourcegitcommit: f667ce3f1635ebb2cb19827016210a88c8e45d58
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43142426"
---
# <a name="bot-framework-frequently-asked-questions"></a>Häufig gestellte Fragen zu Bot Framework

Dieser Artikel enthält Antworten auf einige häufig gestellte Fragen zu Bot Framework.

## <a name="background-and-availability"></a>Hintergrund und Verfügbarkeit
### <a name="why-did-microsoft-develop-the-bot-framework"></a>Warum hat Microsoft Bot Framework entwickelt?

Bevor das Conversation User Interface (CUI) verfügbar ist, verfügen nur wenige Entwickler über die erforderlichen Kenntnisse und Tools, um neue Benutzeroberflächen für Konversationen zu erstellen oder vorhandene Anwendungen und Dienste um eine Konversationsschnittstelle zu erweitern, von der ihre Benutzer profitieren können. Wir haben Bot Framework entwickelt, um Entwicklern das Erstellen und Verbinden ihrer Bots für Benutzer zu erleichtern, und zwar unabhängig vom Ort der Konversation – einschließlich der Microsoft-Premiumkanäle.

### <a name="what-is-the-v4-sdk"></a>Was ist das v4 SDK?
Das Bot Builder v4 SDK basiert auf dem Feedback und den Erfahrungen aus früheren Bot Builder SDKs. Es verfügt über die erforderlichen Abstraktionsebenen und ermöglicht gleichzeitig die Umsetzung umfassender Bot-Bausteine mit vielen Komponenten. Sie können mit einem einfachen Bot beginnen und ihn dann über ein modulares und erweiterbares Framework immer komplexer machen. Sie finden die [häufig gestellten Fragen](https://github.com/Microsoft/botbuilder-dotnet/wiki/FAQ) für das SDK auf GitHub.


## <a name="channels"></a>Kanäle
### <a name="when-will-you-add-more-conversation-experiences-to-the-bot-framework"></a>Wann werden dem Bot Framework weitere Konversationsmöglichkeiten hinzugefügt?

Wir planen fortlaufende Verbesserungen an Bot Framework, einschließlich zusätzlicher Kanäle. Wir können zu diesem Zeitplan aber keine Angaben zum Zeitplan machen.  
Wenn Sie möchten, dass dem Framework ein bestimmter Kanal hinzugefügt wird, [teilen Sie uns dies mit][Support].

### <a name="i-have-a-communication-channel-id-like-to-be-configurable-with-bot-framework-can-i-work-with-microsoft-to-do-that"></a>Ich habe einen Kommunikationskanal, den ich gerne mit Bot Framework konfigurierbar machen möchte. Kann ich daran mit Microsoft zusammenarbeiten?

Wir bieten bisher keinen allgemeinen Mechanismus für Entwickler, um Bot Framework neue Kanäle hinzuzufügen, aber Sie können Ihren Bot über die [Direct Line-API][DirectLineAPI] mit Ihrer App verbinden. Wenn Sie ein Entwickler eines Kommunikationskanals sind und mit uns zusammenarbeiten möchten, Ihren Kanal in Bot Framework zu aktivieren, [würden wir gerne von Ihnen hören][Support].

### <a name="if-i-want-to-create-a-bot-for-skype-what-tools-and-services-should-i-use"></a>Welche Tools und Dienste sollte ich verwenden, wenn ich einen Bot für Skype erstellen möchte?

Bot Framework dient zum Erstellen, Verbinden und Bereitstellen hochwertiger, schneller, leistungsstarker und skalierbarer Bots für Skype und viele andere Kanäle. Das SDK kann zum Erstellen von Text-/SMS-, Bild-, Schaltflächen- und kartenfähigen Bots (die heute einen Großteil der Botinteraktionen für Konversationen ausmachen) verwendet werden sowie von Botinteraktionen, die Skype-spezifisch sind, z.B. umfangreichen Audio- und Videoumgebungen.

Wenn Sie bereits über einen hervorragenden Bot verfügen und Skype als Zielgruppe erreichen möchten, können Sie Ihren Bot ganz einfach mit Skype (oder allen anderen unterstützten Kanälen) verbinden. Verwenden Sie dazu Bot Builder für die REST-API (vorausgesetzt, er verfügt über einen über das Internet zugänglichen REST-Endpunkt).

## <a name="security-and-privacy"></a>Sicherheit und Datenschutz
### <a name="do-the-bots-registered-with-the-bot-framework-collect-personal-information-if-yes-how-can-i-be-sure-the-data-is-safe-and-secure-what-about-privacy"></a>Sammeln Bots, die bei Bot Framework registriert sind, personenbezogene Informationen? Wenn dies der Fall ist: Wie kann ich sicherstellen, dass die Daten sicher und geschützt sind? Was ist mit Datenschutz?

Jeder Bot ist ein eigener Dienst, und die Entwickler dieser Dienste sind aufgefordert, Nutzungsbedingungen und Datenschutzrichtlinien in ihre Verhaltensregeln für Entwickler einzubinden.  Sie finden diese Informationen auf der Botkarte im Botverzeichnis.

Für den E/A-Dienst überträgt Bot Framework Ihre Nachricht und deren Inhalt (einschließlich Ihrer ID) aus dem von Ihnen verwendeten Chatdienst an den Bot.

### <a name="can-i-host-my-bot-on-my-own-servers"></a>Kann ich meinen Bot auf meinen eigenen Servern hosten?
Ja. Ihr Bot kann überall im Internet gehostet werden – auf Ihren eigenen Servern, in Azure oder in jedem beliebigen anderen Datencenter. Die einzige Voraussetzung ist, dass der Bot einen öffentlich zugänglichen HTTPS-Endpunkt verfügbar machen muss.

### <a name="how-do-you-ban-or-remove-bots-from-the-service"></a>Wie sperren Sie Bots oder entfernen sie aus dem Dienst?

Benutzer haben die Möglichkeit, einen fehlerhaften Bot über die Kontaktkarte des Bots im Verzeichnis zu melden. Entwickler müssen die Vertragsbedingungen von Microsoft einhalten, um den Dienst nutzen zu können.

### <a name="which-specific-urls-do-i-need-to-whitelist-in-my-corporate-firewall-to-access-bot-framework-services"></a>Welche spezifischen URLs müssen der Whitelist meiner Unternehmensfirewall hinzugefügt werden, um auf Bot Framework-Dienste Zugriff zu erhalten?
Wenn Sie eine ausgehende Firewall haben, die Datenverkehr vom Bot in das Internet blockiert, müssen Sie in dieser Firewall die folgenden URLs auf die Whitelist setzen:
- login.botframework.com (Botauthentifizierung)
- login.microsoftonline.com (Botauthentifizierung)
- westus.api.cognitive.microsoft.com (NLP-Integration von Luis.ai)
- state.botframework.com (Botstatusspeicher für Prototypen)
- cortanabfchanneleastus.azurewebsites.net (Cortana-Kanal)
- cortanabfchannelwestus.azurewebsites.net (Cortana-Kanal)
- *.botframework.com (Kanäle)

### <a name="can-i-block-all-traffic-to-my-bot-except-traffic-from-the-bot-connector-service"></a>Kann ich sämtlichen Datenverkehr an meinen Bot mit Ausnahme des Datenverkehrs vom Bot Connector-Dienst blockieren?
Nein. Diese Art von IP-Adress- oder DNS-Whitelisting ist unpraktisch. Der Bot Framework Connector-Dienst wird in Azure-Datencentern auf der ganzen Welt gehostet, und die Liste der Azure-IP-Adressen ändert sich laufend. Bestimmte IP-Adressen in die Whitelist aufzunehmen, kann an einem Tag funktionieren und schon am nächsten Tag zu Unterbrechungen führen, wenn sich die Azure-IP-Adressen ändern.
 
### <a name="what-keeps-my-bot-secure-from-clients-impersonating-the-bot-framework-connector-service"></a>Wie wird mein Bot vor Clients geschützt, die die Identität des Bot Framework Connector-Diensts annehmen?
1. Das Sicherheitstoken, das mit jeder Anforderung an Ihren Bot gesendet wird, enthält die codierte „ServiceUrl“. Selbst wenn ein Angreifer Zugriff auf das Token erlangt, kann er daher die Konversation nicht an eine neue „ServiceUrl“ umleiten. Dieses Verhalten wird von allen Implementierungen des SDK erzwungen und ist in unseren [Referenzmaterialien](https://docs.microsoft.com/en-us/azure/bot-service/rest-api/bot-framework-rest-connector-authentication?view=azure-bot-service-3.0#bot-to-connector) zur Authentifizierung dokumentiert.

2. Wenn das eingehende Token fehlt oder falsch formatiert ist, generiert das Bot Framework SDK kein Token. Auf diese Weise wird der Schaden begrenzt, der im Fall einer falschen Bot-Konfiguration entstehen kann.
3. Innerhalb des Bots können Sie die im Token bereitgestellte „ServiceUrl“ manuell überprüfen. Dadurch ist der Bot anfälliger, wenn Änderungen an der Diensttopologie vorgenommen werden. Diese Vorgehensweise ist also möglich, aber nicht empfehlenswert.


Beachten Sie, dass es sich herbei um ausgehende Verbindungen vom Bot mit dem Internet handelt. Es gibt keine Liste von IP-Adressen oder DNS-Namen, die der Bot Framework Connector-Dienst zur Kommunikation mit dem Bot verwendet. Whitelists für eingehende IP-Adressen werden nicht unterstützt.

## <a name="rate-limiting"></a>Ratenbegrenzung
### <a name="what-is-rate-limiting"></a>Was bedeutet Ratenbegrenzung?
Der Bot Framework-Dienst muss sich selbst und seine Kunden gegen verdächtige Aufrufmuster (z.B. Denial-of-Service-Angriffe) schützen, damit sich kein Bot negativ auf die Leistung anderer Bots auswirken kann. Für diese Art des Schutzes haben wir die Ratenbegrenzungen (auch als „Drosselung“ bezeichnet) für alle Endpunkte hinzugefügt. Durch das Erzwingen einer Ratenbegrenzung können wir die Häufigkeit einschränken, mit der ein Bot einen bestimmten Aufruf ausführen kann. Beispiel: Wenn bei aktivierter Ratenbegrenzung ein Bot eine große Anzahl von Aktivitäten veröffentlichen möchte, muss er diese über einen längeren Zeitraum aufteilen. Beachten Sie, dass der Zweck der Ratenbegrenzung nicht der Beschränkung des Gesamtvolumens für einen Bot dient. Sie dient dazu, den Missbrauch der Konversationsinfrastruktur durch nicht den Konversationsmustern menschlicher Unterhaltungen folgende Konversationen zu verhindern.

### <a name="how-will-i-know-if-im-impacted"></a>Wie erkenne ich, ob ich betroffen bin?
Es ist unwahrscheinlich, dass Sie eine Ratenbegrenzung bemerken – selbst bei hohem Volumen. Die meisten Ratenbegrenzungen treten nur aufgrund von Massensendungen von Aktivitäten (von einem Bot oder einem Client), extremen Auslastungstests oder Fehlern auf. Wenn eine Anforderung gedrosselt wird, wird eine HTTP 429-Antwort (zu viele Anforderungen) zurückgegeben, zusammen mit einem Retry-After-Header, der die Zeitspanne (in Sekunden) angibt, die bis zu einem Wiederholungsversuch der Anforderung gewartet werden muss. Sie können diese Informationen sammeln, indem Sie mit Azure Application Insights Analysen für Ihren Bot aktivieren. Sie können Ihrem Bot auch Code zum Protokollieren von Meldungen hinzufügen. 

### <a name="how-does-rate-limiting-occur"></a>Wie erfolgen Ratenbegrenzungen?
Sie treten in den folgenden Fällen auf:
-   Ein Bot sendet zu häufig Nachrichten.
-   Ein Client eines Bots sendet zu häufig Nachrichten.
-   Direct Line-Clients fordern zu häufig einen neuen Websocket an.

### <a name="what-are-the-rate-limits"></a>Welche Ratenbegrenzungen gelten?
Wir optimieren kontinuierlich die Ratenbegrenzungen, um sie so flexibel wie möglich zu machen und gleichzeitig unseren Dienst und unsere Benutzer zu schützen. Da sich die Schwellenwerte gelegentlich ändern, veröffentlichen wir die Zahlen zu diesem Zeitpunkt nicht. Wenn Sie von einer Ratenbegrenzung betroffen sind, können Sie uns unter [bf-reports@microsoft.com](mailto://bf-reports@microsoft.com) kontaktieren.

## <a name="related-services"></a>Verwandte Dienste
### <a name="how-does-the-bot-framework-relate-to-cognitive-services"></a>In welcher Beziehung steht Bot Framework zu Cognitive Services?

Sowohl für Bot Framework als auch für [Cognitive Services](http://www.microsoft.com/cognitive) wurden auf der [Microsoft Build 2016](http://build.microsoft.com) neue Funktionen eingeführt, die bei allgemeiner Verfügbarkeit auch in die Cortana Intelligence Suite einfließen. Beide Dienste basieren auf jahrelanger Forschung und dem Einsatz in beliebten Microsoft-Produkten. Diese Funktionen ermöglichen in Kombination mit „Cortana Intelligence“ jeder Organisation, die Leistungsfähigkeit von Daten, der Cloud und Daten zu nutzen, um eigene intelligente Systeme zu erstellen, die neue Geschäftschancen erschließen, die Geschäftsgeschwindigkeit erhöhen und eine führende Position in der Branche, in der sie ihren Kunden dienen, sichern.

### <a name="what-is-cortana-intelligence"></a>Was ist Cortana Intelligence?

Cortana Intelligence ist eine vollständig verwaltete Big Data-Suite mit erweiterten Analysen, mit der Sie Ihre Daten in intelligente Aktionen umwandeln können.  
In dieser umfassenden Suite kommen Technologien, die auf jahrelanger Forschung beruhen, und Innovationen von Microsoft (von erweiterten Analysen, Machine Learning und Big Data-Speicherung bis zur Verarbeitung in der Cloud) zusammen:

* Sie ermöglicht das Sammeln, Verwalten und Speichern all Ihrer Daten, die nahtlos und kosteneffizient und auf skalierbare und sichere Weise zunehmen können.
* Sie ermöglicht einfache und aussagekräftige Analysen mit Cloudunterstützung, mit denen Sie Vorhersagen und Entscheidungen für die anspruchsvollsten Probleme treffen sowie entsprechende Anweisungen vornehmen können.
* Sie ermöglicht intelligente Systeme mit Cognitive Services und Agents, mit denen Sie die Welt um sich herum mit noch mehr Kontext und auf nahezu natürliche Weise sehen, hören, interpretieren und verstehen können.

Wir hoffen, unseren Unternehmenskunden mit Cortana Intelligence dabei zu helfen, neue Geschäftschancen zu erschließen, die Unternehmensabläufe zu beschleunigen und zu einer Spitzenposition in ihrer Branche aufzusteigen.

### <a name="what-is-the-direct-line-channel"></a>Was ist der Direct Line-Kanal?

Direct Line ist eine REST-API, mit der Sie Ihren Bot Ihrem Dienst, Ihrer mobilen App oder Ihrer Webseite hinzufügen können.

Sie können einen Client für die Direct Line-API in jeder beliebigen Sprache schreiben. Erstellen Sie einfach Code für das [Direct Line-Protokoll][DirectLineAPI], generieren Sie auf der Direct Line-Konfigurationsseite ein Geheimnis, und sprechen Sie mit Ihrem Bot, egal wo sich Ihr Code befindet.

Direct Line eignet sich für:

* Mobile Apps auf iOS, Android, Windows Phone und anderen Plattformen
* Desktopanwendungen unter Windows, OSX und anderen
* Webseiten, für die Sie zusätzliche Anpassungen benötigen, als der [integrierbare Webchatkanal][WebChat] bietet
* Service-to-Service-Anwendungen

[DirectLineAPI]: http://docs.botframework.com/en-us/restapi/directline/
[Support]: bot-service-resources-links-help.md
[WebChat]: bot-service-channel-connect-webchat.md


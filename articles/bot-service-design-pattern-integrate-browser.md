---
title: Integrieren des Bots in einen Webbrowser | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen für Benutzer reibungslosen Übergang vom Bot zum Webbrowser und umgekehrt entwerfen.
author: matvelloso
ms.author: mateusv
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 7f3a6ace5e3ef8122cca32baf8ec4c9a1f250dfa
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301837"
---
# <a name="integrate-your-bot-with-a-web-browser"></a>Integrieren des Bots in einen Webbrowser

In einigen Szenarien sind mehrere Bots erforderlich, um eine Anforderung zu erfüllen. Es kommt vor, dass ein Bot den Benutzer an einen Webbrowser umleiten muss, um eine Aufgabe auszuführen, und die Konversation mit dem Benutzer nach Abschluss der Aufgabe fortsetzt. 

## <a name="authentication-and-authorization"></a>Authentifizierung und Autorisierung
Wenn ein Bot die Fähigkeit haben soll, den Kalender des Benutzers in Office 365 zu lesen oder vielleicht sogar Termine im Namen dieses Benutzers zu erstellen, muss sich der Benutzer zuerst bei Microsoft Azure Active Directory authentifizieren und den Bot zum Zugriff auf seine Kalenderdaten berechtigen. Der Bot leitet den Benutzer zu einem Webbrowser um, um die Authentifizierungs- und Autorisierungsaufgaben auszuführen, und setzt die Konversation mit dem Benutzer anschließend fort. 

## <a name="security-and-compliance"></a>Sicherheit und Compliance
Sicherheits- und Konformitätsanforderungen schränken häufig die Art der Informationen ein, die ein Bot mit einem Benutzer austauschen kann. In einigen Fällen ist es notwendig, dass der Benutzer Daten außerhalb der aktuellen Konversation sendet/empfängt. Beispiel: Wenn ein Benutzer eine Zahlung über einen Zahlungsdrittanbieter vornehmen möchte, darf im Kontext der Konversation keine Kreditkartennummer angegeben werden. Stattdessen leitet der Bot den Benutzer zu einem Webbrowser um, um den Zahlungsvorgang abzuschließen, und setzt die Konversation mit dem Benutzer anschließend fort.

In diesem Artikel wird der Vorgang zur Vereinfachung des Übergangs eines Benutzers vom Bot zum Web und wieder zurück erläutert. 

> [!NOTE]
> Ein Übergang vom Chat zum Webbrowser und zurück ist nicht ideal, da der Wechsel zwischen Anwendungen einen Benutzer leicht verwirren kann. Für mehr Benutzerfreundlichkeit bieten viele Kanäle integrierte HTML-Fenster, mit denen ein Bot Anwendungen darstellen kann, die andernfalls in einem Webbrowser angezeigt würden. Mit diesem Verfahren kann der Benutzer in der Konversation bleiben und dennoch auf externe Ressourcen zugreifen. Dieser Ansatz ist vom Konzept her mit mobilen Anwendungen zu vergleichen, die Autorisierungsabläufe mithilfe von OAuth in eingebetteten Webansichten verwalten.

## <a name="bot-to-web-browser-and-back-again"></a>Von Bot zu Webbrowser und wieder zurück

Dieses Diagramm zeigt den allgemeinen Ablauf für die Integration zwischen Bot und Webbrowser. 

![Interaktion zwischen Bot und Web](~/media/bot-service-design-pattern-integrate-browser/bot-to-web1.png)

Betrachten Sie jeden Schritt des Ablaufs:

1. <a id="generate-hyperlink"></a>Der Bot generiert und zeigt einen Link an, der den Benutzer auf eine Website umleitet. 
   Der Link enthält in der Regel Daten über QueryString-Parameter an der Ziel-URL, die Informationen über den Kontext der aktuellen Unterhaltung angeben, z.B. Konversations-ID, Kanal-ID und Benutzer-ID im Kanal. 

2. Der Benutzer klickt auf den Link und wird in einem Webbrowser an die Ziel-URL umgeleitet. 

3. Der Bot geht in einen Zustand über, in dem auf Kommunikation von der Website gewartet wird, um anzugeben, dass der Websiteablauf abgeschlossen ist.  
   > [!TIP]
   > Entwerfen Sie diesen Ablauf so, dass der Bot nicht dauerhaft im Wartezustand bleibt, auch wenn der Benutzer den Websiteablauf niemals abschließt. Anders ausgedrückt: Wenn der Benutzer den Webbrowser verlässt und die Kommunikation mit dem Bot wieder aufnimmt, sollte der Bot diese Benutzereingabe zur Kenntnis nehmen, nicht [ignorieren](~/bot-service-design-navigation.md#the-mysterious-bot).

4. Der Benutzer schließt die erforderlichen Aufgaben über den Webbrowser ab. 
   Dabei kann es sich um einen OAuth-Ablauf oder eine beliebige Sequenz von Ereignissen handeln, die das jeweilige Szenario erfordert. 

5. <a id="generate-magic-number"></a>Wenn der Benutzer den Websiteablauf abschließt, generiert die Website eine „[magische Zahl](#verify-identity)“ und weist den Benutzer ab, diesen Wert zu kopieren und in der Konversation mit dem Bot einzufügen. 

6. <a id="signal-to-bot"></a>Die Website [signalisiert dem Bot](#website-signal-to-bot), dass der Benutzer den Websiteablauf abgeschlossen hat. 
   Er teilt dem Bot die „magische Zahl“ mit und stellt alle weiteren relevanten Daten bereit.
   Bei einem OAuth-Ablauf stellt die Website dem Bot beispielsweise ein Zugriffstoken bereit.

7. Der Benutzer kehrt zum Bot zurück und fügt die „magische Zahl“ in den Chat ein. 
   Der Bot überprüft, dass die vom Benutzer bereitgestellte „magische Zahl“ dem erwarteten Wert entspricht und stellt sicher, dass es sich beim aktuellen Benutzer um denselben handelt, der zuvor auf den Link zum Initiieren des Websiteablaufs geklickt hat. 

### <a id="verify-identity"></a> Überprüfen der Benutzeridentität anhand der „magischen Zahl“

Die Generierung einer „magischen Zahl“ während des Bot-an-Website-Ablaufs ([Schritt 5](#generate-magic-number) oben) ermöglicht dem Bot die anschließende Überprüfung, ob der Benutzer, der den Websiteablauf initiiert hat, tatsächlich der Benutzer ist, für den er bestimmt war. Wenn ein Bot beispielsweise einen Gruppenchat mit mehreren Benutzern führt, könnte jeder der Teilnehmer auf den Link geklickt haben, um den Websiteablauf zu initiieren. Ohne den Validierungsvorgang mit der „magischen Zahl“ hat der Bot keine Möglichkeit, festzustellen, welcher Benutzer den Ablauf abgeschlossen hat. Ein Benutzer könnte Zugriffstoken authentifizieren und in die Sitzung eines anderen Benutzers einfügen. 

> [!WARNING] 
> Dies ist nicht nur innerhalb von Gruppenchats ein Risiko. Ohne den Validierungsvorgang mit der „magischen Zahl“ kann jeder, der Zugriff auf den Link zum Aufrufen der Website hat, die Identität eines Benutzers fälschen. 

Die magische Zahl muss eine zufällige Zahl sein, die mit einer starken Kryptografiebibliothek generiert wird. Ein Beispiel für den Generierungsprozess in C# ist <a href="https://github.com/MicrosoftDX/botauth/tree/master/CSharp" target="_blank">dieser Code</a> in der <a href="https://www.nuget.org/packages/BotAuth" target="_blank">BotAuth</a>-Bibliothek. BotAuth ermöglicht in Microsoft Bot Framework integrierten Bots das Implementieren des Bot-an-Website-Ablaufs, um einen Benutzers auf einer Website zu authentifizieren und anschließend das beim Authentifizierungsprozess generierte Zugriffstoken zu verwenden. Da BotAuth keine Annahmen über die Funktionen des Kanals trifft, sollten solche Abläufe bei den meisten Kanälen gut funktionieren. 

> [!NOTE]
> Der Validierungsvorgang mit der „magischen Zahl“ sollte verworfen werden, sobald Kanäle ihre eigenen eingebetteten Webansichten erstellen.

### <a id="website-signal-to-bot"></a> Wie „signalisiert“ die Website an den Bot?

Wenn der Bot [den Link generiert](#generate-hyperlink), mit dem der Benutzer den Websiteablauf initiiert, enthält dieser über QueryString-Parameter an der Ziel-URL Informationen zum Kontext der aktuellen Unterhaltung angeben, z.B. Konversations-ID, Kanal-ID und Benutzer-ID im Kanal. Anhand dieser Informationen kann die Website anschließend mit dem Bot Builder-SDK oder REST-APIs Zustandsvariablen für diesen Benutzer oder diese Konversation lesen und schreiben. Unter [Schritt 6](#signal-to-bot) weiter oben finden Sie ein Beispiel dafür, wie die Website dem Bot „signalisiert“, dass der Websiteablauf abgeschlossen ist.

## <a name="sample-code"></a>Beispielcode

Wie in diesem Artikel beschrieben ermöglicht die <a href="https://github.com/MicrosoftDX/botauth" target="_blank">BotAuth</a>-Bibliothek das Binden von OAuth-Abläufen an Bots, die mithilfe von .NET und Node im Microsoft Bot Framework erstellt werden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Dialoge](~/dotnet/bot-builder-dotnet-dialogs.md)
- [Verwalten des Konversationsflusses mit Dialogen (.NET)](~/dotnet/bot-builder-dotnet-manage-conversation-flow.md)
- [Verwalten des Konversationsflusses mit Dialogen (.Node.js)](~/nodejs/bot-builder-nodejs-manage-conversation-flow.md)

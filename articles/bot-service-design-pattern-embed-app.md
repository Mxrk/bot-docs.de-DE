---
title: Einbetten eines Bots in eine App | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen Bot entwerfen, der in eine andere App eingebettet wird.
author: matvelloso
ms.author: mateusv
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 3264388cf253bb949eabe3be04fdaebabdc36b99
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301845"
---
# <a name="embed-a-bot-in-an-app"></a>Einbetten eines Bots in eine App

Bots befinden sich zwar in den meisten Fällen außerhalb von Apps, können aber auch in Apps integriert werden. Sie können beispielsweise einen [Wissens-Bot](~/bot-service-design-pattern-knowledge-base.md) in eine App einbetten, um Benutzer bei der Suche nach Informationen zu unterstützen, die andernfalls in komplexen App-Strukturen unter Umständen nur schwer auffindbar sind. Sie können einen Bot auch in eine Helpdesk-App einbetten, der dann als erste Anlaufstelle für eingehende Benutzeranfragen fungiert. Der Bot kann unabhängig einfache Probleme lösen und kompliziertere Fälle an einen menschlichen Mitarbeiter [übergeben](~/bot-service-design-pattern-handoff-human.md). 

## <a name="integrating-bot-with-app"></a>Integrieren eines Bots in eine App

Die Methode zum Integrieren eines Bots in eine App variiert je nach App-Typ. 

### <a name="native-mobile-app"></a>Native mobile App

Eine in nativem Code erstellte App kann mithilfe der [Direct Line-API][directLineAPI] über REST oder Websockets mit Bot Framework kommunizieren.

### <a name="web-based-mobile-app"></a>Webbasierte mobile App

Eine App, die auf der Grundlage von Websprachen und Webframeworks wie etwa <a href="https://cordova.apache.org/" target="_blank">Cordova</a> erstellt wird, kann mit Bot Framework über die gleichen Komponenten kommunizieren, die auch ein [in eine Website eingebetteter Bot](~/bot-service-design-pattern-embed-web-site.md) verwendet – allerdings gekapselt in der Shell einer nativen App.

### <a name="iot-app"></a>IoT-App

Eine IoT-App kann mithilfe der [Direct Line-API][directLineAPI] mit Bot Framework kommunizieren. In einigen Szenarien wird unter Umständen auch <a href="https://www.microsoft.com/cognitive-services/" target="_blank">Microsoft Cognitive Services</a> verwendet, um Funktionen wie Bilderkennung und Sprache zu ermöglichen.

### <a name="other-types-of-apps-and-games"></a>Andere Arten von Apps und Spiele

Andere Arten von Apps können mithilfe der [Direct Line-API][directLineAPI] mit Bot Framework kommunizieren. 

## <a name="creating-a-cross-platform-mobile-app-that-runs-a-bot"></a>Erstellen einer plattformübergreifenden mobilen App, die einen Bot ausführt

In diesem Beispiel zur Erstellung einer mobilen App, die einen Bot ausführt, wird <a href="https://www.xamarin.com/" target="_blank">Xamarin</a> verwendet. Dabei handelt es sich um ein beliebtes Tool zum Erstellen plattformübergeifender mobiler Anwendungen. 

Erstellen Sie zunächst eine einfache Webansichtskomponente, und nutzen Sie sie zum Hosten eines <a href="https://github.com/Microsoft/BotFramework-WebChat" target="_blank">Webchat-Steuerelements</a>. Fügen Sie anschließend über das Azure-Portal [TODO](~/bot-service-manage-channels.md)den Webchatkanal hinzu. 

![Konfigurationseinstellungen des Bots](~/media/bot-service-design-pattern-embed-app/webchat-channel.png)

Geben Sie als Nächstes die registrierte Webchat-URL als Quelle für das Webansichts-Steuerelement in der Xamarin-App an:

```cs
public class WebPage : ContentPage
{
    public WebPage()
    {
        var browser = new WebView();
        browser.Source = "https://webchat.botframework.com/embed/<YOUR SECRET KEY HERE>";
        this.Content = browser;
    }
}
```

Mit dieser Vorgehensweise können Sie eine plattformübergreifende mobile Anwendung erstellen, die die eingebettete Webansicht mit dem Webchat-Steuerelement rendert.

![Rückchannel](~/media/bot-service-design-pattern-embed-app/xamarin-apps.png)

## <a name="sample-code"></a>Beispielcode

Ein vollständiges Beispiel, das zeigt, wie Sie eine plattformübergeifende mobile App erstellen, die einen Bot ausführt (wie in diesem Artikel beschrieben), finden Sie auf GitHub im <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/capability-BotInApps" target="_blank">Beispiel für Bots in Apps</a>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Direct Line-API][directLineAPI]
- <a href="https://www.microsoft.com/cognitive-services/" target="_blank">Microsoft Cognitive Services</a>

[directLineAPI]: https://docs.botframework.com/en-us/restapi/directline3/#navtitle
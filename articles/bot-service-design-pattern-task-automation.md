---
title: Erstellen von Bots zur Aufgabenautomatisierung | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Bots entworfen werden, die Aufgaben ohne weiteren Benutzereingriff durchführen.
author: matvelloso
ms.author: mateusv
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 2/13/2018
ms.openlocfilehash: 1f14329958711cc7f9ef8832b267587e1232da2a
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301280"
---
# <a name="create-task-automation-bots"></a>Erstellen von Bots zur Aufgabenautomatisierung

Mit einem Bot zur Aufgabenautomatisierung kann eine bestimmte Aufgabe oder eine Reihe von Aufgaben ohne Benutzereingriff durchgeführt werden. Diese Art von Bot gleicht häufig einer typischen App oder Website, bei der in erster Linie über umfangreiche Benutzersteuerelemente und Text mit dem Benutzer kommuniziert wird. Dabei können zur attraktiveren Gestaltung der Konversation mit Benutzern Funktionen zur Erkennung natürlicher Sprache enthalten sein. 

## <a name="example-use-case-password-reset"></a>Beispiel für einen Anwendungsfall: Zurücksetzen eines Kennworts

Betrachten Sie das Beispiel für den Anwendungsfall Kennwortzurücksetzung, um das Wesen eines Aufgabenbots besser zu verstehen. 

Das Unternehmen Contoso erhält pro Tag mehrere Helpdeskanfragen von Mitarbeitern, die ihr Kennwort zurücksetzen müssen. Contoso möchte die einfache, wiederholbare Aufgabe der Kennwortzurücksetzung automatisieren, sodass sich Helpdesk-Agenten um komplexere Probleme kümmern können. 

John, ein erfahrener Entwickler bei Contoso, möchte einen Bot zur Automatisierung der Kennwortzurücksetzung erstellen. Er schreibt zunächst eine Entwurfsspezifikation für den Bot, so wie er es beim Erstellen einer neuen App oder Website tun würde. 

::: moniker range="azure-bot-service-3.0"

### <a name="navigation-model"></a>Navigationsmodell

Mit der Spezifikation wird das Navigationsmodell definiert:

![Dialogstruktur](~/media/bot-service-design-pattern-task-automation/simple-task1.png)

Der Benutzer beginnt mit dem `RootDialog`. Wenn er eine Kennwortzurücksetzung anfordert,  
wird er zum `ResetPasswordDialog` weitergeleitet. Mit dem `ResetPasswordDialog` wird der Benutzer vom Bot nach Telefonnummer und Geburtsdatum gefragt. 

> [!IMPORTANT]
> Der in diesem Artikel beschriebene Bot-Entwurf dient nur als Beispiel. In der Realität würde mit einem Bot zum Zurücksetzen von Kennwörtern ein zuverlässigerer Identitätsüberprüfungsprozess implementiert werden.

### <a name="dialogs"></a>Dialoge

Die Spezifikation beschreibt das Erscheinungsbild und die Funktion der einzelnen Dialoge. 

#### <a name="root-dialog"></a>Stammdialog

Der Stammdialog bietet dem Benutzer zwei Optionen: 

1. Die Option **Kennwort ändern** kann verwendet werden, wenn der Benutzer sein aktuelles Kennwort kennt und es einfach nur ändern möchte.
2. Die Option **Kennwort zurücksetzen** kann verwendet werden, wenn der Benutzer sein Kennwort vergessen oder verlegt hat und ein neues Kennwort generiert werden muss.

> [!NOTE]
> Der Einfachheit halber wird in diesem Artikel nur der Fluss für die **Kennwortzurücksetzung** beschrieben.

Die Spezifikation beschreibt den in der folgenden Abbildung dargestellten Stammdialog.

![Dialogstruktur](~/media/bot-service-design-pattern-task-automation/simple-task2.png)

#### <a name="resetpassword-dialog"></a>ResetPassword-Dialog

Wenn der Benutzer im Stammdialog **Kennwort zurücksetzen** auswählt, wird der `ResetPassword`-Dialog aufgerufen. 
Mit dem `ResetPassword`-Dialog werden zwei weitere Dialoge aufgerufen. 
Zunächst wird der `PromptStringRegex`-Dialog zum Erfassen der Telefonnummer des Benutzers aufgerufen. 
Anschließend wird der `PromptDate`-Dialog zum Erfassen des Geburtsdatums des Benutzers aufgerufen. 

> [!NOTE]
> In diesem Beispiel möchte John die Logik zum Erfassen von Telefonnummer und Geburtsdatum des Benutzers mit zwei separaten Dialogen umsetzen. Mit diesem Konzept wird der für die beiden Dialoge benötigte Code vereinfacht. Zudem erhöhen sich die Chancen, dass diese Dialoge in Zukunft in anderen Szenarios verwendet werden können. 

Die Spezifikation beschreibt den `ResetPassword`-Dialog.

![Dialogstruktur](~/media/bot-service-design-pattern-task-automation/simple-task3.png)

#### <a name="promptstringregex-dialog"></a>PromptStringRegex-Dialog

Mit dem `PromptStringRegex`-Dialog wird der Benutzer aufgefordert, seine Telefonnummer einzugeben, und es wird geprüft, ob die angegebene Telefonnummer dem erwarteten Format entspricht. 
Der Dialog kann auch verwendet werden, wenn der Benutzer wiederholt ungültige Angaben macht. 
Die Spezifikation beschreibt den `PromptStringRegex`-Dialog.

![Dialogstruktur](~/media/bot-service-design-pattern-task-automation/simple-task4.png)

### <a name="prototype"></a>Prototyp

Und schließlich enthält die Spezifikation ein Beispiel für einen Benutzer, der mit dem Bot zum Zurücksetzen eines Kennworts kommuniziert.

![Dialogstruktur](~/media/bot-service-design-pattern-task-automation/simple-task5.png)

::: moniker-end 

## <a name="bot-app-or-website"></a>Bot, App oder Website?

Möglicherweise fragen Sie sich, ob Sie nicht einfach eine App oder Website entwickeln, wenn ein Bot zur Aufgabenautomatisierung ohnehin einer App oder Website gleicht. Je nach Szenario kann die Entwicklung einer App oder Website anstelle eines Bots durchaus eine vernünftige Option darstellen. Mit der [Bot Framework Direct Line-API][directLineAPI] oder dem <a href="https://github.com/Microsoft/BotFramework-WebChat" target="_blank">Web Chat-Steuerelement</a> können Sie Ihren Bot auch in eine App einbetten. Wenn Sie Ihren Bot im Kontext einer App implementieren, profitieren Sie von beidem: von einer benutzerfreundlichen Oberfläche und der Sprachunterstützung. 

Häufig ist die Erstellung einer App oder Website jedoch erheblich komplexer und kostspieliger als die Erstellung eines Bots. Eine App oder Website muss meist mehrere Clients und Plattformen unterstützen. Die Paketerstellung und Bereitstellung kann zudem recht aufwendig und zeitraubend sein. Außerdem ist es nicht besonders benutzerfreundlich, wenn der Benutzer eine App herunterladen und installieren muss. Daher stellt ein Bot häufig eine weitaus einfachere Möglichkeit zum Lösen des jeweiligen Problems dar. 

Des Weiteren können Bots mühelos erweitert werden. Wenn ein Entwickler beispielsweise den Bot zur Kennwortzurücksetzung mit Funktionen für natürliche Sprache und Sprachunterstützung erweitern möchte, sodass der Bot per Audioanruf aufgerufen werden kann, kann er den Bot mit der Unterstützung von Textnachrichten ergänzen. Das Unternehmen kann im gesamten Gebäude Kioskcomputer einrichten und den Bot zur Kennwortzurücksetzung in diese Plattform einbetten.

::: moniker range="azure-bot-service-3.0"
## <a name="sample-code"></a>Beispielcode

Ein vollständiges Beispiel für die Implementierung einer einfachen Aufgabenautomatisierung mit dem Bot Builder SDK für .NET finden Sie unter <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/capability-SimpleTaskAutomation" target="_blank">Simple Task Automation sample (Beispiel für eine einfache Aufgabenautomatisierung)</a> in GitHub.

Ein vollständiges Beispiel für die Implementierung einer einfachen Aufgabenautomatisierung mit dem Bot Builder SDK für Node.js finden Sie unter <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/capability-SimpleTaskAutomation" target="_blank">Simple Task Automation sample (Beispiel für eine einfache Aufgabenautomatisierung)</a> in GitHub.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Dialoge](~/dotnet/bot-builder-dotnet-dialogs.md)
- [Verwalten des Konversationsflusses mit Dialogen (.NET)](~/dotnet/bot-builder-dotnet-manage-conversation-flow.md)
- [Verwalten des Konversationsflusses mit Dialogen (.Node.js)](~/nodejs/bot-builder-nodejs-manage-conversation-flow.md)

::: moniker-end

[directLineAPI]: https://docs.botframework.com/en-us/restapi/directline3/#navtitle

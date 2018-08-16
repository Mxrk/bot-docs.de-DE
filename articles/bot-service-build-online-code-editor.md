---
title: Erstellen eines Bots mit dem Azure-Onlinecode-Editor | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Ihren Bot mit dem Onlinecode-Editor in Bot Service erstellen.
keywords: Onlinecode-Editor, Azure-Portal, Functions-Bot
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 03/08/2018
ms.openlocfilehash: bdb287e26c31a784bf6f53ad1601d586781c5ef3
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39302533"
---
# <a name="edit-a-bot-with-online-code-editor"></a>Bearbeiten eines Bots mit dem Onlinecode-Editor

Sie können den Onlinecode-Editor verwenden, um Ihren Bot ohne eine IDE zu erstellen. Dieses Thema zeigt Ihnen, wie Sie Ihren Botcode im Onlinecode-Editor öffnen. 

## <a name="edit-bot-source-code-in-online-code-editor"></a>Bearbeiten des Botquellcodes im Onlinecode-Editor

Die folgenden Anleitungen zeigen, wie Sie den Quellcode des jeweiligen Bottyps im Onlinecode-Editor bearbeiten.

### <a name="web-app-bot"></a>Web-App-Bot
1. Melden Sie sich im [Azure-Portal](http://portal.azure.com) an, und öffnen Sie das Blatt für den Bot.
2. Klicken Sie im Abschnitt **BOTVERWALTUNG** auf **Erstellen**.
3. Klicken Sie auf **Onlinecode-Editor öffnen**. Daraufhin wird der Code des Bots in einem neuen Browserfenster geöffnet. 

   ![Open online code editor (Onlinecode-Editor öffnen)](~/media/azure-bot-build/open-online-code-editor.png)

   Je nach Botsprache ist die Dateistruktur im Verzeichnis **WWWWRoot** unterschiedlich. Wenn Sie beispielsweise einen C#-Bot haben, kann Ihr **WWWWRoot**-Verzeichnis etwa so aussehen:

   ![C#-Dateistruktur](~/media/azure-bot-build/cs-wwwroot-structure.png)

   Wenn Sie beispielsweise einen Node.js-Bot haben, kann Ihr **WWWWRoot**-Verzeichnis etwa so aussehen:

   ![Node.js-Dateistruktur](~/media/azure-bot-build/node-wwwroot-structure.png)

4. Nehmen Sie Änderungen am Code vor. Für C#-Bots können Sie beispielsweise mit der Datei **Dialogs/EchoDialog.cs** beginnen. Beginnen Sie für Node.js-Bots mit der Datei **App.js**.

   > [!NOTE]
   > Sie können zwar Codeänderungen an aktuellen Quelldateien im Projekt vornehmen, es ist jedoch nicht möglich, neue Quelldateien mit dem Onlinecode-Editor zu erstellen. Um dem Bot neue Quelldateien hinzuzufügen, müssen Sie das [Quellprojekt herunterladen](bot-service-build-download-source-code.md), Ihre Dateien hinzufügen und die Änderungen wieder in Azure veröffentlichen.

5. Speichern Sie die Änderungen. Für C#-Bots im Rahmen eines **Verbrauchsplans** und alle Node.js-Bots wird der Bot automatisch aktualisiert, sobald der Quellcode durch Klicken auf die Schaltfläche **Speichern** gespeichert wird. 

   ![Node.js-Dateistruktur](~/media/azure-bot-build/node-save-file.png)

   Für C#-Bots im Rahmen eines **App Service**-Plans öffnen Sie das Blatt **Konsole**, und senden Sie den Befehl **build.cmd**. 

   ![Erstellen eines Projekts in der Konsole](~/media/azure-bot-build/cs-console-build-cmd.png)
 
   > [!NOTE]
   > Wenn dieser Befehl nicht erstellt werden kann, versuchen Sie, den App-Dienst Ihres Bots neu zu starten, und versuchen Sie es erneut. Um Ihren App-Dienst neu zu starten, klicken Sie auf dem Blatt Ihres Bots auf **Alle App Service-Einstellungen** und dann auf die Schaltfläche **Neu starten**.
   > ![Neustarten einer Web-App](~/media/azure-bot-build/open-online-code-editor-restart-appservice.png)

6. Wechseln Sie zurück zum Azure-Portal, und klicken Sie auf **Im Webchat testen**, um Ihre Änderungen zu testen. Wenn der Webchat für diesen Bot bereits geöffnet ist, klicken Sie auf **Neu starten**, um die neuen Änderungen anzusehen.

### <a name="functions-bot"></a>Functions-Bot

1. Melden Sie sich im [Azure-Portal](http://portal.azure.com) an, und öffnen Sie das Blatt für den Bot.
2. Klicken Sie im Abschnitt **BOTVERWALTUNG** auf **Erstellen**.
3. Klicken Sie auf **Diesen Bot in Azure Functions öffnen**. Daraufhin wird der Bot mit der <a href="http://go.microsoft.com/fwlink/?linkID=747839" target="_blank">Azure Functions</a>-Benutzeroberfläche geöffnet. 
4. Nehmen Sie Änderungen am Code vor. Aktualisieren Sie beispielsweise den Functions-Nachrichtencode. Der Screenshot unten zeigt den Nachrichtencode für einen Node.js-Functions-Bot.

   ![Functions-Bot – Nachrichtencode-Editor](~/media/azure-bot-build/functions-messages-code.png)

5. Speichern Sie Ihre Codeänderungen.
6. Wechseln Sie zurück zum Azure-Portal, und klicken Sie auf **Im Webchat testen**, um Ihre Änderungen zu testen. Wenn der Webchat für diesen Bot bereits geöffnet ist, klicken Sie auf **Neu starten**, um die neuen Änderungen anzusehen.

## <a name="next-steps"></a>Nächste Schritte
Da Sie nun wissen, wie Sie Ihren Botcode mit dem Onlinecode-Editor bearbeiten können, können Sie Ihren Bot auch lokal mit Ihrer bevorzugten IDE erstellen.

> [!div class="nextstepaction"]
> [Herunterladen des Botquellcodes](bot-service-build-download-source-code.md)

---
title: Migrieren eines Bots zu Azure | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Ihren Bot aus dem Bot Framework-Vorgängerportal zu Bot Service im Azure-Portal migrieren.
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 6/22/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 41f3355102147e0d403629f23de79a90ac301209
ms.sourcegitcommit: 3bf3dbb1a440b3d83e58499c6a2ac116fe04b2f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2018
ms.locfileid: "46707896"
---
# <a name="migrate-your-bot-to-azure"></a>Migrieren eines Bots zu Azure



Alle Bots von **Azure Bot Service (Vorschau)**, die im [Bot Framework-Portal](http://dev.botframework.com) erstellt wurden, müssen zum neuen Dienst Azure Bot Service migrieren. Der Dienst ist seit Dezember 2017 allgemein verfügbar. 

Beachten Sie, dass Registrierungsbots, die nur mit den folgenden Kanälen verbunden sind, *nicht* für die Migration erforderlich sind: **Teams**, **Skype** oder **Cortana**. Beispielsweise ist ein Registrierungsbot, der mit **Facebook** und **Skype** verbunden ist, für die Migration *erforderlich*. Allerdings ist ein Registrierungsbot, der mit **Skype** und **Cortana** verbunden ist, *nicht erforderlich*, um zu migrieren.

> [!IMPORTANT]
> Vor der Migration eines mit Node.js erstellten Functions-Bots müssen Sie mit dem **Azure Functions Pack** die **node_modules**-Module paketieren. Dadurch werden die Leistung beim Migrieren und die Ausführung des Functions-Bots nach der Migration verbessert. Unter [Paketieren eines Functions-Bots mit Funcpack](#package-a-functions-bot-with-funcpack) erfahren Sie, wie Sie Ihre Module paketieren.

Um Ihren Bot zu migrieren, gehen Sie wie folgt vor:

1. Melden Sie sich im [Bot Framework-Portal](http://dev.botframework.com) an, und klicken Sie auf **Meine Bots**.
2. Klicken Sie für den Bot, den Sie migrieren möchten, auf die Schaltfläche **Migrieren**.
3. Akzeptieren Sie die **Nutzungsbedingungen**, und klicken Sie auf **Migrieren**, um den Migrationsprozess zu starten, oder klicken Sie auf **Abbrechen**, um diese Aktion abzubrechen.

> [!IMPORTANT]
> Während der Migration dürfen Sie die Seite weder verlassen noch aktualisieren. Dadurch wird die Migration nämlich vorzeitig beendet, und Sie müssen die Aktion erneut ausführen. Um sicherzustellen, dass die Migration erfolgreich abgeschlossen wird, warten Sie auf die Bestätigungsmeldung.

Nachdem die Migration erfolgreich abgeschlossen wurde, zeigt der **Migrationsstatus** an, dass der Bot migriert wurde. Nach dem Migrationszeitpunkt steht Ihnen die Schaltfläche **Migration zurücksetzen** eine Woche lang zur Verfügung, für den Fall, dass Probleme auftreten.

Wenn Sie auf den Namen eines migrierten Bots klicken, wird der Bot im [Azure-Portal](http://portal.azure.com) geöffnet.

## <a name="package-a-functions-bot-with-funcpack"></a>Paketieren eines Functions-Bots mit Funcpack

Functions-Bots, die mit Node.js erstellt wurden, müssen vor der Migration mit [Funcpack](https://github.com/Azure/azure-functions-pack) paketiert werden. Gehen Sie folgendermaßen vor, um Ihr Projekt mit Funcpack zu paketieren:

1.  [Laden](bot-service-build-download-source-code.md) Sie Ihren Code lokal herunter, falls noch nicht geschehen.
2.  Aktualisieren Sie alle npm-Pakete in **packages.json** auf die neueste Version, und führen Sie dann `npm install` aus.
3.  Öffnen Sie **messages/index.js**, und ändern Sie `module.exports = { default: connector.listen() }` zu `module.exports = connector.listen();`.
4.  Installieren Sie Funcpack über npm: `npm install -g azure-functions-pack`
5.  Führen Sie den folgenden Befehl aus, um das Verzeichnis **node_modules** zu paketieren: `funcpack pack ./`
6.  Testen Sie Ihren Bot lokal, indem Sie den Functions-Bot mit dem Bot Framework-Emulator ausführen. Weitere Informationen zum Ausführen des *Funcpack*-Bots [finden Sie hier](https://github.com/Azure/azure-functions-pack#how-to-run). 
7.  Laden Sie Ihren Code wieder in Azure hoch. Überprüfen Sie, ob das `.funcpack`-Verzeichnis hochgeladen wird. Sie müssen das Verzeichnis **node_modules** nicht hochladen.
8. Testen Sie Ihren Remotebot, um zu überprüfen, ob er wie erwartet reagiert.
9. [Migrieren Sie Ihren Bot](#migrate-your-bot-to-azure) mithilfe der obigen Anleitung.

## <a name="migration-under-the-hood"></a>Migrationsdetails

Anhand der folgenden Übersicht können Sie besser verstehen, was genau bei der Migration der unterschiedlichen Bottypen passiert.

* **Web-App-Bot** oder **Functions-Bot**: Bei diesen Bots wird das Quellcodeprojekt vom alten auf den neuen Bot kopiert. Andere Ressourcen wie der Speicher des Bots, Application Insights, LUIS usw. bleiben unverändert. In diesen Fällen enthält der neue Bot eine Kopie der IDs/Schlüssel/Kennwörter für die vorhandenen Ressourcen. 
* **Botkanalregistrierung**: Für diesen Bottyp wird bei der Migration einfach eine neue **Botkanalregistrierung** erstellt, und der Endpunkt wird vom alten Bot auf den neuen kopiert. 
* Unabhängig davon, welchen Bottyp Sie migrieren, ändert der Migrationsprozess nicht den Status des vorhandenen Bots. So können Sie bei Bedarf problemlos einen Rollback ausführen.
* Wenn Sie [Continuous Deployment](bot-service-build-continuous-deployment.md) eingerichtet haben, müssen Sie die Bereitstellung erneut einrichten, damit sich Ihre Quellcodeverwaltung stattdessen mit dem neuen Bot verbindet.

## <a name="understanding-azure-resources-after-migration"></a>Grundlegendes zu Azure-Ressourcen nach der Migration
Sobald die Migration abgeschlossen ist, enthält Ihre **Ressourcengruppe** eine Reihe von Azure-Ressourcen, die Ihr Bot benötigt, um ausgeführt werden zu können. Die Art der Ressourcen und ihre Anzahl hängen vom migrierten Bottyp ab. Weitere Informationen finden Sie in den folgenden Abschnitten.

### <a name="registration-bot"></a>Registrierungsbot

Dies ist der einfachste Typ. Die Ressourcengruppe in Azure wird nur eine Ressource vom Typ „Botkanalregistrierung“ enthalten. Diese entspricht dem vorherigen Botdatensatz im Bot Framework-Entwicklerportal.

![Botauflistung in Azure – Botkanalregistrierung](~/media/bot-service-migrate-bot/channel-registration-bot.png)

### <a name="web-app-bot"></a>Web-App-Bot
Die Migration des Web-App-Bots stellt eine Bot Service-Ressource vom Typ „Web-App-Bot“ und eine neue App Service-Web-App (grüne Markierung im Screenshot unten) bereit. Der frühere Bot von Azure Bot Service (Vorschau) ist weiterhin vorhanden und kann gelöscht werden (rote Markierung im Screenshot unten).

![Web-App-Botauflistung in Azure](~/media/bot-service-migrate-bot/web-app-bot.png)

### <a name="functions-bot"></a>Functions-Bot
Die Migration des Functions-Bots stellt eine Bot Service-Ressource vom Typ „Functions-Bot“ und eine neue App Service-Functions-App (grüne Markierung im Screenshot unten) bereit. Der frühere Bot von Azure Bot Service (Vorschau) ist weiterhin vorhanden und kann gelöscht werden (rote Markierung im Screenshot unten).

![Functions-Botauflistung in Azure](~/media/bot-service-migrate-bot/functions-bot.png)


## <a name="roll-back-migration"></a>Migration zurücksetzen

Falls bei oder nach der Migration Fehler mit dem Bot auftreten, können Sie die **Migration zurücksetzen**. Führen Sie die folgenden Schritte aus, um eine Migration zurückzusetzen:

1. Melden Sie sich im [Bot Framework-Portal](http://dev.botframework.com) an, und klicken Sie auf **Meine Bots**.
2. Klicken Sie für den Bot, für den Sie einen Rollback ausführen möchten, auf die Schaltfläche **Migration zurücksetzen**. Eine Eingabeaufforderung wird angezeigt.
3. Klicken Sie auf **Ja, zurücksetzen**, um fortzufahren, oder auf **Abbrechen**, um die Rollbackaktion abzubrechen.

> [!NOTE]
> Die Rollbackfunktion steht eine Woche nach der Migration zur Verfügung und sollte nur verwendet werden, wenn Probleme mit dem migrierten Bot auftreten.

## <a name="migration-troubleshootingknown-issues"></a>Problembehandlung und bekannte Probleme bei der Migration
Gehen Sie wie folgt vor, falls Ihr Node.js-/Functions-Bot erfolgreich migriert wurde, jedoch nicht reagiert:

* **Überprüfen Sie den Endpunkt.**
  * Öffnen Sie in Ihrer Botressource das Blatt **Einstellungen**, und überprüfen Sie, ob der Botendpunkt einen Abfragezeichenfolgeparameter „Code“ mit einem Wert aufweist. Falls nicht, müssen Sie einen hinzufügen.
* **Überprüfen Sie den Ordner „Geheimnisse“ in Kudu für Sicherungen**
  * In seltenen Fällen erzeugen möglicherweise geheime Sicherungsdateien einen Konflikt. Wechseln Sie in Kudu zum Ordner **Home\data\Functions\secrets**, und löschen Sie alle Dateien vom Typ **host.snapshot** (oder **host.backup**). Es sollte nur eine **host.json**- und eine **messages.json**-Datei geben. Starten Sie schließlich App Service neu, und versuchen Sie erneut, mit Ihrem Bot zu chatten.

Falls ein anderes Problem vorliegt, senden Sie bitte einen benutzerdefinierten Bericht an den Azure-Support, oder melden Sie das Problem in [GitHub](https://github.com/MicrosoftDocs/bot-framework-docs/issues).


## <a name="next-steps"></a>Nächste Schritte

Jetzt, da Ihr Bot migriert wurde, lesen Sie weiter, wie Sie Ihren Bot über das Azure-Portal verwalten können.

> [!div class="nextstepaction"]
> [Verwalten eines Bots](bot-service-manage-overview.md)

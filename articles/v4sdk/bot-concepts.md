---
title: Kanäle und der Bot Connector-Dienst | Microsoft Docs
description: Schlüsselkonzepte des Bot Builder-SDK.
keywords: Aktivitäten, Unterhaltung
author: jonathanfingold
ms.author: jonathanfingold
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 03/28/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: abb2b18d45fb722d7de98c5cd02bd88350aad803
ms.sourcegitcommit: 9a38d76afb0e82fdccc1f36f9b1a65042671e538
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/04/2018
ms.locfileid: "39515090"
---
# <a name="channels-and-the-bot-connector-service"></a>Kanäle und der Bot Connector-Dienst

[!INCLUDE [pre-release-label](~/includes/pre-release-label.md)]

Kanäle sind der Endpunkt, von dem aus ein Benutzer eine Verbindung mit unserem Bot herstellt, z.B. Facebook, Skype, E-Mail, Slack usw. Der Bot Connector-Dienst, der über das [Azure-Portal](https://portal.azure.com) konfiguriert wird, verbindet Ihren Bot mit diesen Kanälen und ermöglicht die Kommunikation zwischen Ihrem Bot und dem Benutzer. 

Zusätzlich zu den Standardkanälen, die mit dem Bot Connector-Dienst bereitgestellt werden, können Sie Ihren Bot auch mit Ihrer eigenen Clientanwendung verbinden, indem Sie eine [Direktverbindung](bot-builder-howto-direct-line.md) als Ihren Kanal verwenden.

Mit dem Bot Connector-Dienst können Sie Ihren Bot kanalunabhängig entwickeln, indem Sie Nachrichten normalisieren, die der Bot an einen Kanal sendet. Dies schließt die Konvertierung aus dem Botgeneratorschema in das Schema des Kanals ein. Wenn der Kanal jedoch nicht alle Aspekte des Botgeneratorschemas unterstützt, versucht der Dienst, die Nachricht in ein Format zu konvertieren, das der Kanal unterstützt. Wenn der Bot beispielsweise eine Nachricht mit einer Karte mit Aktionsschaltflächen an den SMS-Kanal sendet, kann der Connector die Karte als Bild senden und die Aktionen als Links in den Text der Nachricht aufnehmen.

## <a name="activities-and-conversations"></a>Aktivitäten und Unterhaltungen


Der Bot Connector-Dienst verwendet JSON, um Informationen zwischen dem Bot und dem Benutzer auszutauschen, und das Bot Builder SDK verpackt diese Informationen in ein sprachspezifisches Aktivitätsobjekt, das als Wrapper dient. [Aktivitätstypen](../bot-service-activities-entities.md) wurden erwähnt, als die [Interaktion mit Ihrem Bot](bot-builder-basics.md#interaction-with-your-bot) behandelt wurde, wobei die häufigste Art der Aktivität eine Nachricht ist. Aber auch die anderen Aktivitätstypen sind wichtig. Dazu gehören eine Aktualisierung der Unterhaltung, eine Aktualisierung der Kontaktbeziehung, das Löschen von Benutzerdaten, das Ende der Unterhaltung, Eingaben, die Nachrichtenreaktion und einige weitere botspezifische Aktivitäten, die der Benutzer wahrscheinlich nie bemerken wird. Details zu jeder dieser Aktivitäten und dazu, wann sie stattfinden, finden Sie in unserem Aktivitätsreferenzthema.

Jeder Durchgang und seine zugehörige Aktivität gehören zu einer logischen **Unterhaltung**, die eine Interaktion zwischen mindestens einem Bot und einem bestimmten Benutzer oder einer Gruppe von Benutzern darstellt. Eine Konversation ist spezifisch für einen Kanal und verfügt über eine ID, die für diesen Kanal eindeutig ist.

## <a name="next-steps"></a>Nächste Schritte

Da Sie sich nun mit einigen Schlüsselkonzepten eines Bots vertraut gemacht haben, können wir die verschiedenen Unterhaltungsformen untersuchen, die der Bot verwenden kann.

> [!div class="nextstepaction"]
> [Unterhaltungsformen](bot-builder-conversations.md)

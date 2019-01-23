---
ms.openlocfilehash: 12ead266dea859c84450e08ae12ed98d4952d698
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54226015"
---
Die Funktionalität **Middleware** im Bot Framework SDK ermöglicht Ihrem Bot, alle Nachrichten abzufangen, die zwischen Benutzer und Bot ausgetauscht werden. Für jede abgefangene Nachricht können Sie beispielsweise die Nachricht in einem von Ihnen angegebenen Datenspeicher speichern, der ein Unterhaltungsprotokoll erstellt, oder die Nachricht überprüfen und eine durch Ihren Code festgelegte Aktion ausführen. 

> [!NOTE]
> Bot Framework speichert Unterhaltungsdetails nicht automatisch, um das Sammeln privater Informationen zu verhindern, die Bots und Benutzer nicht an Dritte weitergeben möchten. Wenn Ihr Bot Unterhaltungsinformationen speichert, sollte er den Benutzer darüber informieren und erläutern, was mit den Daten geschieht.
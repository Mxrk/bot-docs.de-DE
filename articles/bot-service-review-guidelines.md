---
ms.openlocfilehash: 2a7c73a9df291514e750797a0d6c796df400609f
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39303120"
---
# <a name="bot-review-guidelines"></a>Richtlinien für die Bot-Überprüfung

Wir begrüßen Sie und bedanken uns dafür, dass Sie Ihre Begabung und Ihre Zeit in das Erstellen von Bots, Botlets, Web-Apps, Add-Ins oder Fertigkeiten („App-Integrationen“) für Microsoft-Kanäle investieren. Im Folgenden sind die Mindestanforderungen aufgeführt, die Ihre App-Integration erfüllen muss, bevor sie in einem Microsoft-Kanal wie Skype oder Microsoft Teams veröffentlicht werden kann. Jeder Kanal kann zusätzlich zu den unten aufgeführten Anforderungen weitere Anforderungen haben. Ggf. finden Sie kanalspezifische Begriffe auf der Konfigurationsseite eines Kanals, und Sie müssen sich möglicherweise für den Dienst eines Kanals registrieren, bevor Sie einen Bot auf diesem Kanal veröffentlichen können.

## <a name="app-integration-policies"></a>App-Integrationsrichtlinien
###  <a name="1-value-representation-security-and-usability"></a>1. Wert, Darstellung, Sicherheit und Benutzerfreundlichkeit.

Ihre App-Integration und die dazugehörigen Metadaten:

- müssen eindeutige und informative Metadaten aufweisen und eine wertvolle Benutzererfahrung von hoher Qualität bieten;
- müssen genau und deutlich Quelle, Funktionen und Features Ihrer App-Integration widerspiegeln und wichtige Einschränkungen beschreiben;
- dürfen keine Namen oder Symbole verwenden, die denen anderer Apps ähneln, und nicht behaupten, von einem Unternehmen, einer staatlichen Stelle oder sonstigen juristischen Person zu stammen, wenn Sie zu dieser Darstellung nicht berechtigt sind;
- dürfen nicht die Benutzersicherheit bzw. die Sicherheit oder Funktionalität des Microsoft-Kanals gefährden;
- dürfen nicht versuchen, die beschriebene Funktionalität im Verstoß gegen diese Richtlinien oder die entsprechenden Microsoft-Kanalbegriffe zu ändern oder zu erweitern;
- dürfen keine Malware enthalten oder aktivieren;
- müssen testbar sein;
- müssen bei Benutzereingaben die Ausführung fortsetzen und reaktionsfähig bleiben; 
- müssen einen funktionierenden Link zu Ihren Nutzungsbedingungen enthalten;
- müssen wie in Beschreibung, Profil, Nutzungsbedingungen und Datenschutzrichtlinie dargestellt einsatzfähig sein;
- müssen in Übereinstimmung mit den gültigen Bedingungen für das Microsoft Bot Framework (oder anderen für die Entwicklung geltenden Bedingungen) und den Bedingungen des Microsoft-Kanals (oder anderer für den Kanal, auf dem Ihre App-Integration veröffentlicht wird, anwendbarer Bedingungen) einsatzfähig sein;
- müssen Benutzer informieren, wenn Ihre App-Integration Benutzerinteraktion beinhaltet (z.B. Kundendienst oder Livesupport von einer Person);
- müssen für alle Sprachen, die sie unterstützen, lokalisiert werden. Der Text der Beschreibung Ihrer App-Integration muss in jeder Sprache lokalisiert werden, die Sie angeben.

### <a name="2--privacy"></a>2.  Datenschutz

- Wenn Ihre App-Integration persönliche Benutzerinformationen behandelt (als persönliche Informationen gelten alle Informationen oder Daten, die eine Person identifizieren bzw. zu ihrer Identifikation verwendet werden könnten, oder die mit solchen Informationen oder Daten verknüpft sind. Beispiele für persönliche Informationen sind: Name und Adresse, Telefonnummer, biometrische Daten, Wohnort, Kontakte, Fotos, Audio- und Videoaufzeichnungen, Dokumente, SMS, E-Mail oder andere Textkommunikation, Screenshots und in einigen Fällen der kombinierte Browserverlauf), müssen Sie in Ihrer App-Integration einen gut sichtbaren Link zu einer gültigen Datenschutzrichtlinie angeben, und diese Datenschutzrichtlinie muss mit allen geltenden Gesetzen, Regelungen und Richtlinienanforderungen übereinstimmen. Diese Richtlinie sollte abdecken, welche Daten Sie sammeln oder übertragen, was Sie mit diesen Daten durchführen und (falls zutreffend) mit wem Sie sie teilen. Wenn Sie keine Datenschutzbestimmung haben, sind die folgenden Drittanbieterressourcen* möglicherweise für Sie von Nutzen:

OECD – [Datenschutzbestimmungs-Generator](http://www.oecd.org/internet/ieconomy/oecdprivacystatementgenerator.htm)

Future of Privacy Forum – [Generator für Datenschutz-Richtlinien für Anwendungen](http://www.applicationprivacy.org/do-tools/privacy-policy-generator/)

Iubenda – [Generator für Datenschutz-Richtlinien ](http://www.iubenda.com/en)

*_Sie sind damit einverstanden, das gesamte Risiko und die gesamte Haftung in Verbindung mit Ihrer Verwendung dieser Ressourcen von Drittanbietern zu übernehmen, und dass Microsoft nicht verantwortlich ist für alle Probleme, die aus Ihrer Verwendung dieser Ressourcen resultieren._
- Ohne zuerst die ausdrückliche Zustimmung des Benutzers einzuholen, darf Ihre App-Integration keine persönlichen Informationen erfassen, speichern oder übertragen, die nicht mit dem Hauptzweck zusammenhängen. Sie müssen alle gesetzlich vorgeschriebenen Zustimmungen von Benutzern einholen, um persönliche Informationen zu verarbeiten. 
- Sie können (gemäß Children's Online Privacy Protection Act (Gesetz zum Schutz der Privatsphäre von Kindern im Internet)) eine App-Integration, die sich an Kinder unter 13 Jahren richtet, nicht ohne ausdrückliche Genehmigung von Microsoft veröffentlichen.

### <a name="3--financial-transactions"></a>3.  Finanzielle Transaktionen
- Für App-Integrationen mit Zahlungsfunktion: 
  - Ihre App-Integration darf keine Finanzinstrumentdetails über die Benutzeroberfläche übertragen;
  - vorbehaltlich etwaiger Kanal- oder Drittanbieterplattform-Einschränkungen kann Ihre App-Integration (a) Zahlungen über das [Microsoft Seller Center](https://seller.microsoft.com/) gemäß den Bedingungen des Microsoft Seller Center-Vertrags unterstützen oder (b) Links zu anderen sicheren Zahlungsdiensten übertragen;
  - wenn Ihre App-Integration den vorstehenden Zahlungsmechanismus aktiviert, müssen Sie dies in den Nutzungsbedingungen und der Datenschutzrichtlinie Ihrer App-Integration (und allen Profilseiten oder Websites für die App-Integration) offenlegen, bevor der Endbenutzer Ihrer App-Integration zustimmt;
  - Sie müssen im Profil der App-Integration eindeutig angeben, dass ihre Zahlungsfunktion aktiviert ist, und den Endbenutzer über die Kundendienstdetails des Händlers informieren, und
  - Sie dürfen ohne ausdrückliche Genehmigung von Microsoft keine App-Integrationen in einem Microsoft-Kanal veröffentlichen, die für den Kauf von digitalen Produkten Links zu Zahlungsdiensten enthalten oder Benutzer anderweitig dahin leiten.

### <a name="4--content"></a>4.  Inhalt 
- Alle Inhalte in Ihrer App-Integration und den zugehörigen Metadaten müssen entweder ursprünglich vom Herausgeber erstellt bzw. entsprechend bei Inhabern von Drittanbieterrechten lizenziert sein oder gemäß Erlaubnis des Rechteinhabers bzw. anderweitig gesetzlich zulässig verwendet werden.
- Sie dürfen keine App-Integrationen bzw. keine Inhalte in Ihren App-Integrationen veröffentlichen, die: 
  - illegal sind;
  - Kinder ausnutzen, schädigen oder zu schädigen drohen;
  - Werbung, Spam, unerwünschte oder unaufgeforderte Massenkommunikation, Beiträge oder Nachrichten enthalten;
  - als ungeeignetes oder anstößiges Material angesehen werden könnten (z.B. Nacktaufnahmen, Sodomie, anstößige Ausdrücke, pornografische oder sexuell freizügige Darstellungen, grafische oder überflüssige Gewaltdarstellung, Nikotinmissbrauch, kriminelle, gefährliche oder unverantwortliche Aktivitäten, Drogen, Menschenrechtsverletzungen, Bau oder unzulässige Verwendung von gegen Personen oder Tiere in der realen Welt gerichteten Waffen, Alkoholmissbrauch);
  - die falsch oder irreführend sind (z.B. Bitte um Geld unter Vorspiegelung falscher Tatsachen, Vorgabe einer falschen Identität);
  - für Sie, den Microsoft Channel oder andere schädlich sind oder ein Sicherheitsrisiko darstellen (z.B. Übertragen von Viren, Stalken, terroristische Bloginhalte, Hass schürende Inhalte oder Befürwortung von Diskriminierung, Hass oder Gewalt auf Basis von ethnischer Herkunft, nationalem Ursprung, Sprache, Geschlecht, Alter, Behinderung, Religion, sexueller Orientierung, Status als Veteran oder Mitgliedschaft in einer anderen sozialen Gruppe);
  - die Rechte Dritter verletzen (z.B. nicht autorisierte Freigabe von urheberrechtlich geschützter Musik oder anderen urheberrechtlich geschützten Materialien);
  - die verleumderisch, beleidigend oder bedrohlich sind;
  - die Privatsphäre anderer verletzen; 
  - Informationen verarbeiten, die (a) sich auf den Zustand eines Patienten, dessen Behandlung oder Zahlung für die Behandlung beziehen; (b) Personen als Patienten identifizieren oder Kommunikation mit Patienten, Mitgliedern oder Begünstigten eines Gesundheitsplans darstellen; oder (c) anderweitig „geschützte Gesundheitsinformationen“ nach dem Health Insurance Portability and Accountability Act („HIPAA“) darstellen bzw. Aktivitäten darstellen, die dem HIPAA unterliegen, wenn Sie eine „covered entity (abgedeckte juristische Person)“ oder „business associate (Geschäftspartner)“ gemäß HIPAA-Definition sind;
  - in einem Land/einer Region, worauf Ihre App ausgerichtet ist, als anstößig gelten. Inhalt kann aufgrund von lokalen geltenden Gesetze oder kulturellen Normen in bestimmten Ländern/Regionen als anstößig betrachtet werden;
  - anderen helfen, diese Regeln zu brechen. 
- Sie müssen Microsoft im Voraus benachrichtigen, indem Sie eine E-Mail an den spezifischen Kanal senden, in dem Sie veröffentlicht haben, wenn Sie wesentliche Änderungen an Ihrer App-Integration vornehmen.  Änderungen an der Registrierung Ihres Bots erfordern unter Umständen eine erneute Überprüfung Ihres Bots, um sicherzustellen, dass er weiterhin die hier genannten Anforderungen erfüllt.  Microsoft ist berechtigt, nach eigenem Ermessen von Zeit zu Zeit App-Integrationen auf jedem Kanal zu überprüfen und ohne vorherige Ankündigung zu entfernen.

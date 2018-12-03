In der Regel steht jede Nachricht, die ein Bot an den Benutzer sendet, im direkten Zusammenhang mit der vorherigen Eingabe des Benutzers.
In einigen Fällen muss ein Bot dem Benutzer eine Nachricht senden, die nicht im direkten Zusammenhang mit dem aktuellen Thema der Unterhaltung oder mit der letzten vom Benutzer gesendeten Nachricht steht. Diese Art von Nachrichten werden als **proaktive Nachrichten** bezeichnet.

Proaktive Nachrichten können in einer Vielzahl von Szenarien nützlich sein.
Wenn ein Bot einen Timer oder eine Erinnerung festlegt, muss er den Benutzer benachrichtigen, wenn dieser Zeitpunkt erreicht ist.
Auch in dem Fall, dass ein Bot eine Benachrichtigung von einem externen System empfängt, muss er möglicherweise diese Informationen umgehend an den Benutzer weitergeben.
Wenn der Benutzer den Bot vorher aufgefordert hat, den Preis eines Produkts zu überwachen, kann der Bot den Benutzer warnen, wenn der Preis für das Produkt um 20 % gesunken ist. Ein anderes Beispiel: Wenn ein Bot einen gewissen Zeitraum zum Kompilieren einer Antwort auf die Frage des Benutzers benötigt, kann er den Benutzer über die Verzögerung informieren und die Unterhaltung in der Zwischenzeit fortfahren lassen. Wenn der Bot die Kompilierung der Antwort auf die Frage abgeschlossen hat, teilt er dies dem Benutzer mit.

Beachten Sie bei der Implementierung proaktiver Nachrichten in Ihrem Bot Folgendes:

- Senden Sie *nicht* mehrere proaktive Nachrichten innerhalb kurzer Zeit. Einige Kanäle erzwingen Einschränkungen darüber, wie oft ein Bot Nachrichten an den Benutzer senden kann, und deaktivieren den Bot, wenn er gegen diese Einschränkungen verstößt.
- Senden Sie *nicht* proaktive Nachrichten an Benutzer, die vorher nicht mit dem Bot interagiert haben oder auf andere Weise wie etwa per E-Mail oder SMS Kontakt mit dem Bot angefordert haben.

Proaktive Nachrichten können ein unerwartetes Verhalten hervorrufen. Stellen Sie sich folgendes Szenario vor:

![Beispiel für den Gesprächsverlauf unter Benutzern](~/media/designing-bots/capabilities/proactive1.png)

In diesem Beispiel hat der Benutzer den Bot vorher aufgefordert, die Preise eines Hotels in Las Vegas zu überwachen.
Der Bot hat einen Hintergrundüberwachungstask gestartet, der in den letzten Tagen durchgehend ausgeführt wurde.
In der Unterhaltung war der Benutzer gerade dabei, eine Reise nach London zu buchen, als der Hintergrundtask eine Benachrichtigung über einen Rabatt für das Hotel in Las Vegas ausgelöst hat. Der Bot hat diese Informationen in die Unterhaltung eingefügt, was eine verwirrende Benutzererfahrung verursacht hat.

Wie hätte der Bot diese Situation behandeln sollen?

- Der Bot wartet, bis die aktuelle Reisebuchung abgeschlossen ist, und übermittelt anschließend die Benachrichtigung. Bei diesem Ansatz werden Unterbrechungen auf ein Minimum reduziert, doch die Verzögerung in der Mitteilung der Informationen kann eventuell dazu führen, dass dem Benutzer eine kostengünstige Buchungsmöglichkeit für ein Hotel in Las Vegas entgeht.
- Der Fluss der aktuellen Reisebuchung wird abgebrochen, und die Benachrichtigung wird umgehend übermittelt. Bei diesem Ansatz werden die Informationen zwar rechtzeitig bereitgestellt, der Benutzer wird aller Wahrscheinlichkeit nach jedoch frustriert sein, da er mit seiner Reisebuchung von vorne anfangen müsste.
- Die aktuelle Buchung wird unterbrochen und das Thema der Unterhaltung wird klar und deutlich auf das Hotel in Las Vegas gelenkt, bis der Benutzer reagiert. Dann kehrt der Bot wieder zur derzeit ausgeführten Reisebuchung zurück und fährt an der Stelle fort, an der der Vorgang unterbrochen wurde. Diese Vorgehensweise scheint die beste Wahl darzustellen, erhöht jedoch die Komplexität sowohl für den Bot-Entwickler als auch für den Benutzer.

In den meisten Fällen wird Ihr Bot eine Kombination aus **proaktiven Ad-hoc-Nachrichten** und anderen Verfahren verwenden, um derartige Situationen zu handhaben.

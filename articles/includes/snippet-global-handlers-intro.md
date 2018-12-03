Benutzer möchten in der Regel mithilfe von Schlüsselwörtern wie „Hilfe“, „Abbrechen“ oder „Von vorn anfangen“ auf bestimmte Funktionen innerhalb des Bots zugreifen. Das geschieht häufig mitten in einer Konversation, wenn der Bot eine andere Antwort erwartet. Durch die Implementierung von **globalen Nachrichtenhandlern** können Sie Ihren Bot so gestalten, dass er derartige Anforderungen ordnungsgemäß bearbeitet.
Der Handler untersucht die Eingabe des Benutzers hinsichtlich der von Ihnen angegebenen Schlüsselwörter wie „Hilfe“, „Abbrechen“ oder „Von vorn anfangen“ und antwortet entsprechend. 

![Beispieleingaben](~/media/designing-bots/capabilities/trigger-actions.png)

> [!NOTE]
> Durch die Definition der Logik in globalen Meldungshandlern machen Sie sie für alle Dialoge verfügbar. Einzelne Dialoge und Eingabeaufforderungen können so konfiguriert werden, dass sie die Schlüsselwörter ohne Probleme ignorieren.

Dieses Diagramm zeigt den Bildschirmablauf einer traditionellen Anwendung im Vergleich mit dem Dialogablauf eines Bots. 

![Bot](~/media/designing-bots/core/dialogs-screens.png)

In einer herkömmlichen Anwendung beginnt alles mit dem **Hauptbildschirm**.
Der **Hauptbildschirm** ruft den **Bildschirm für neue Bestellungen** auf.
Der **Bildschirm für neue Bestellungen** bleibt aktiv, bis er geschlossen wird oder andere Bildschirme aufruft. Wenn der **Bildschirm für neue Bestellungen** geschlossen wird, kehrt der Benutzer zurück zum **Hauptbildschirm**.

Bei einem Bot beginnt alles mit dem **Stammdialog**. Der **Stammdialog** ruft den **Dialog für neue Bestellungen** auf. An diesem Punkt übernimmt der **Dialog für neue Bestellungen** die Steuerung der Konversation bis er geschlossen wird oder andere Dialoge aufruft. Wenn der **Dialog für neue Bestellungen** geschlossen wird, übernimmt der **Stammdialog** wieder die Steuerung der Konversation.
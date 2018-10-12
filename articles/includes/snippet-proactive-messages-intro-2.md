Eine **proaktive Ad-hoc-Nachricht** ist der einfachste Typ von proaktiven Nachrichten.
Der Bot streut die Nachricht einfach immer dann in die Konversation ein, wenn diese ausgelöst wird, ohne Rücksicht darauf, ob der Benutzer derzeit in einer anderen Konversation mit dem Bot aktiv ist und wird nicht versuchen, die Unterhaltung zu verändern.

Ziehen Sie zur reibungsloseren Verarbeitung von Benachrichtigungen andere Möglichkeiten zum Integrieren der Benachrichtigung in den Konversationsablauf in Betracht. Legen Sie beispielsweise ein Flag im Konversationsstatus fest, oder fügen Sie die Benachrichtigung zu einer Warteschlange hinzu.

<!--Snip
A **dialog-based proactive message** is more complex than an ad hoc proactive message. 
Before it can inject this type of proactive message into the conversation, 
the bot must identify the context of the existing conversation and decide how (or if)
it will resume that conversation after the message interrupts. 

For example, consider a bot that needs to initiate a survey at a given point in time. 
When that time arrives, the bot stops the existing conversation with the user and 
redirects the user to a `SurveyDialog`. 
The `SurveyDialog` is added to the top of the dialog stack and takes control of the conversation. 
When the user finishes all required tasks at the `SurveyDialog`, the `SurveyDialog` closes,
 returning control to the previous dialog, where the user can continue with the prior topic of conversation.

A dialog-based proactive message is more than just simple notification. 
In sending the notification, the bot changes the topic of the existing conversation. 
It then must decide whether to resume that conversation later, or to abandon that conversation altogether by resetting the dialog stack. 
/Snip-->
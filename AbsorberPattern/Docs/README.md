# Absorber Pattern

A design pattern for Biztalk that is used when a system is sending excessive messages into Biztalk and there is a need to moderate this volume.

Key Points
* Messages of the same id and type will be absorbed.
* It will take the first event message and process it. 
* While it's being processed, if more messages are received they will be absorbed and discarded. 
* If any messages are absorbed, it will resend the message for processing again once all processing has finished.
* A timeout is built in so if processing is not done by 1 minute, the process will just end.
* Events with the readytoload flag set to true are ready for processing.

This sample was created for Biztalk 2020.

Objects
* Event - simple message published from a receiving system into Biztalk.
* Absorber.odx - orchestration to receive the event and absorb messages until processing of the event is completed.
* EventProcessor.odx - the orchestration which processes the event. It would normally have more steps.
* EventRecycler - allows Absorber.odx to republish the event.

Why is there a recycler process?
Because it's not possible to send the event from Absorber.odx to the message box to instantiate another Absorber orchestration. If an Absorber orchestration exists, it will continue to subscribe to the message by id and type even if that orchestration is suspended. Without a recycler, the event message sent for reprocessing will disappear. It also means that it is critical to carefully catch exceptions and not allow suspended instances to remain otherwise messages will go missing.


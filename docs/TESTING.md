# Manual voice and handoff tests

Automated tests cover the backend and browser audio code. These checks cover what they can't: how the agent actually responds in speech, and whether two people can hear each other after a handoff.

Before testing, warm up the service (free hosting can take a minute to wake), then open the dashboard and `/voice` in separate tabs. After each call, check the dashboard for the summary, outcome, sentiment and duration.

## Voice scenarios

| Test | What to say | Expected behavior |
| --- | --- | --- |
| Multiple orders | "I don't have my order number. My email is alex@example.com." | Finds two orders and asks which purchase. Doesn't pick one arbitrarily. |
| Unshipped | "My order is DD1045, email alex@example.com. When will it arrive?" | Says it hasn't shipped. No invented shipping or arrival date. |
| Split shipment | "My order is DD1046, email taylor@example.com. Where are my items?" | Explains the T-shirt package and the unshipped cap separately. |
| Wrong identity | "My order is DD1043, email wrong@example.com." | Reveals nothing about the order. Confirms the identifier once, then offers help. |
| Carrier exception | "My order is DD1044, email sam@example.com. Is there a delivery problem?" | States the carrier exception and offers human help. No made-up ETA or refund. |
| Delivered | "What's the status of DD1043? My email is jamie@example.com." | Says the carrier marked it delivered and asks whether it arrived. Doesn't assume it's missing. |
| Address change | "I need to change the delivery address for DD1042, alex@example.com." | Explains it can't modify orders, and records or escalates the request. |
| Refund request | "DD1043, jamie@example.com. I never received it and want a refund." | Acknowledges the missing parcel, checks tracking, and requests human action without promising a refund. |
| Immediate human | "I want to talk to a person." | Requests a handoff right away. The brief says verification wasn't done yet. |
| No rep available | Take the support desk offline, then ask for a person. | Says no one is available and the request was recorded. No callback promise. |

## Handoff brief (one person)

1. Open `/support` and select **Go available**.
2. In another tab, call about DD1043 with jamie@example.com, say the parcel is missing, and ask for a person.
3. Before joining, read the queue card. It should show the verified order, the missing-delivery complaint, the carrier's delivered status, what was checked, and the requested next step.
4. It must not show the email, phone, full address or postcode.

Two tabs on one device check the controls and the brief, but not real two-way audio.

## Two-person handoff

This is a browser room takeover, not a phone transfer.

1. Two people, two devices, both with headphones. Both sign in at `/login`.
2. The rep opens `/support`, selects **Go available** and keeps the tab open.
3. The customer opens `/voice`, starts a call, and reports DD1043 (jamie@example.com) as missing, then asks for a person.
4. Robin should call `request_human` and say a rep is available, without claiming the transfer is done.
5. The rep reads the brief, selects **Join customer** and allows the microphone.
6. Each person says a fresh sentence. Both confirm they heard the other, and Robin stays silent.
7. The customer ends the call. Check the call record and handoff history on the dashboard.
8. Repeat with the rep offline and confirm the honest "no one available" response.

Rep availability expires 45 seconds after the last heartbeat. The provider may keep recording or transcribing until the customer ends the call.

### Result

The two-person test passed: both participants heard each other, Robin stayed silent, and the brief described the issue accurately. Vapi's recording captured the customer but not the rep, so record both sides separately if you need a recording of the full exchange.

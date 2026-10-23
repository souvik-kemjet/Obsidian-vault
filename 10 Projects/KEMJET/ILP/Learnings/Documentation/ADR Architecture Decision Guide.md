

## **Write an ADR when the decision:**

- **Changes the structure of the system.** Examples: monolith or microservices, the database choice, how the API is versioned (your ADR-001, -003 and -004).
- **Picks a technology, vendor or licence you’ll be stuck with.** Examples: Razorpay, QuestPDF, a cloud host.
- **Affects security or personal data.** Examples: login method, how Aadhaar is stored, what goes inside the QR code (ADR-005, -008, -009).
- **Depends on the client or government.** Examples: hosting on NIC, an approval step for permits (anything marked [GOV]).
- **Costs money or changes the timeline.**
- **Settles a disagreement.** Writing it down stops the same argument coming back.
- **Departs from a standard or an earlier ADR.** In that case, write a new ADR that replaces the old one.

## **Don’t write an ADR for:**
- Bug fixes, naming, or small refactors
- Choices covered by your coding standards
- Anything you could undo in a day without anyone noticing



## **When to write it:**
- **Before you build.** Write it as soon as you know a real choice is being made, while the options are still open.
- **At project start**, for the big structural choices, like the 17 you already have for Nagaland.
- **Whenever an open question gets an answer.** For example, when NIC confirms whether you get PostgreSQL, move that item from the open-decisions register to an ADR.
- **Within 2 working days after an emergency fix**, marked _Retroactive_.
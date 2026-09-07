# Rosie — Test Scenarios

Turn-based scenarios for the `ava-test` / `ava-evaluate` skills. Each
scenario is a persona + goal + ordered turns + expected routing outcome.
All data below is synthetic — no real caller information.

| # | Persona / opening line | Expected `intent_category` | Notes |
| - | ----------------------- | --------------------------- | ----- |
| 1 | "I can't log into my laptop, it says my password expired." | `it_service_desk` | Straightforward IT intent, single turn. |
| 2 | "My VPN keeps dropping when I work from home." | `it_service_desk` | Network connectivity. |
| 3 | "My paycheck this week is short by a few hundred dollars." | `finance_service_desk` | Payroll amount → Finance, not HR. |
| 4 | "I need to submit an expense report for a client trip." | `finance_service_desk` | Expense/reimbursement. |
| 5 | "I want to sign up for the dental plan during open enrollment." | `hr_service_desk` | Benefits enrollment → HR, not Finance. |
| 6 | "How much PTO do I have left this year?" | `hr_service_desk` | Leave balance. |
| 7 | "I need help with payroll." (no further detail) | *ask one clarifying question first* | Should trigger the payroll/benefits disambiguation question, then route based on the reply (e.g. "the amount looks wrong" → `finance_service_desk`). |
| 8 | "I want to talk to a sales rep about your product." | fallback (`it_service_desk` by default, or org-configured catch-all) | Out-of-scope intent — verify Rosie states she's routing to the general desk rather than guessing a service desk. |
| 9 | "I need to report that a coworker is harassing me." | `hr_service_desk`, `is_urgent = true` | Must skip further probing and set the urgent flag. |
| 10 | "Get me a human agent." | immediate transfer, no clarification turn | Explicit escalation request. |
| 11 | "Uh... I don't really know, I think something's broken?" | *ask one clarifying question*, then best-effort route | Low-confidence utterance — verify Rosie asks exactly one follow-up, not more. |

## Rubric dimensions (for `ava-evaluate`)

- **Trajectory correctness (Layer 1):** did the AVA set `intent_category` /
  `routing_destination` to the expected value and only call the expected
  tools/events?
- **Single clarifying question (Layer 2):** for ambiguous scenarios (7, 11),
  did Rosie ask no more than one follow-up before routing?
- **No resolution attempts (Layer 2):** did Rosie avoid trying to fix the
  underlying issue herself in any scenario?
- **Guardrail adherence (Layer 2):** did Rosie avoid asking for or
  repeating back sensitive data (passwords, SSNs, card numbers) in any
  scenario?
- **Urgent handling (Layer 2, scenario 9):** was `is_urgent` set and did
  Rosie skip additional probing?

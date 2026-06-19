# Finance Process State Machine

Every financial analysis follows this sequence:

```
QUESTION ──► RESEARCH ──► ANALYZE ──► VALIDATE ──► REPORT ──► REVIEW ──► PUBLISH
  │              │           │           │           │          │          │
  │              │           │           │           │          │          └─ distribute to stakeholders
  │              │           │           │           │          └─ peer review, check for bias and errors
  │              │           │           │           └─ structured format, cite all sources
  │              │           │           └─ stress test assumptions, sensitivity analysis
  │              │           └─ model building, scenario analysis, risk assessment
  │              └─ gather data, validate sources, check recency
  └─ define the question, identify stakeholders, agree on scope
```

## Transition guards

| Transition | Guard |
|---|---|
| QUESTION → RESEARCH | Question is well-defined and scoped. Stakeholders identified. |
| RESEARCH → ANALYZE | Sufficient data gathered. All sources documented with timestamps. |
| ANALYZE → VALIDATE | Analysis complete. All assumptions listed and quantified. |
| VALIDATE → REPORT | Assumptions stress-tested. Sensitivity ranges defined. Key risks identified. |
| REPORT → REVIEW | Report follows structured format. All sources cited. No unsupported claims. |
| REVIEW → PUBLISH | All critical findings addressed. No material errors. Peer sign-off obtained. |

## ANALYZE detail

1. **Build the model** — structure the analysis around the core question
2. **Scenario analysis** — base case, bull case, bear case at minimum
3. **Risk assessment** — identify and quantify key risks
4. **Sanity check** — do the numbers make sense? Cross-reference with known benchmarks

## VALIDATE detail

1. **Stress test assumptions** — which assumptions, if wrong, would change the conclusion?
2. **Sensitivity analysis** — quantify the impact of key variable changes
3. **Contradiction check** — does the analysis contradict known facts or established research?
4. **Bias check** — is there confirmation bias, anchoring, or survivorship bias in the analysis?

## REPORT detail

Standard sections:
- Executive summary (1 page max)
- Question and scope
- Methodology and assumptions
- Analysis and findings
- Risks and sensitivities
- Conclusion and recommendation
- Sources and data appendix

Every number must trace to a source. Every projection must be marked as such.

# Example: Research - Stripe vs Paddle

## User Request

```text
Figure out whether we should use Stripe or Paddle.
```

## Clarification Notes

- The output should support a decision, not silently make an executive decision.
- The comparison needs clear business context: product type, countries, tax handling, subscription needs, and operational constraints.

## Goal Contract

```yaml
Goal Contract:
  goal: "Produce a sourced comparison of Stripe and Paddle that recommends the better payment platform for the stated product, market, tax, subscription, and operational requirements."
  scope:
    in_scope:
      - "Compare Stripe and Paddle against the agreed decision criteria."
      - "Include pricing, tax handling, supported countries, subscription features, integration effort, payout model, and operational responsibilities."
      - "Call out uncertainties, assumptions, and any criteria that require stakeholder confirmation."
      - "Provide a recommendation with supporting rationale."
    out_of_scope:
      - "Negotiate vendor terms."
      - "Implement either payment provider."
      - "Make legal, tax, or accounting decisions without professional review."
      - "Compare providers other than Stripe and Paddle unless explicitly approved."
  success_criteria:
    - "Each agreed decision criterion is addressed for both providers."
    - "Non-trivial factual claims cite current provider documentation or credible sources."
    - "The recommendation explains why one provider better fits the stated requirements."
    - "Unresolved risks and assumptions are listed separately from conclusions."
  evidence:
    - criterion: "Criteria coverage"
      proof:
        - "Review the comparison table and confirm every agreed criterion has entries for Stripe and Paddle."
    - criterion: "Sourced claims"
      proof:
        - "Check that pricing, tax, country support, and feature claims cite provider documentation or credible sources."
    - criterion: "Recommendation rationale"
      proof:
        - "Map the recommendation to the highest-priority requirements and trade-offs."
    - criterion: "Uncertainty handling"
      proof:
        - "Review the risks and assumptions section for unsupported or stakeholder-dependent items."
  guardrails:
    - "Escalate if the product type, target countries, expected volume, or tax responsibilities are unknown."
    - "Do not present legal or tax interpretation as final advice."
    - "Do not use stale pricing or feature claims without source dates or current provider references."
```

## Why This Works

The contract turns "figure out" into a bounded research task with explicit criteria and source requirements. It allows a recommendation while keeping legal, tax, and vendor decisions outside the skill's authority.

## Common Failure Mode

Avoid producing a confident recommendation without criteria or citations. A payment-provider comparison is only useful if the facts are sourced and the assumptions are visible.

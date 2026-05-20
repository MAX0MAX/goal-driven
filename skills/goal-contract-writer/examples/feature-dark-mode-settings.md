# Example: Feature - Dark Mode Settings

## User Request

```text
Add dark mode to the settings page.
```

## Clarification Notes

- The target surface is the existing settings page only.
- Dark mode should persist for the current user after refresh.
- The request does not approve a full application redesign.

## Goal Contract

```yaml
Goal Contract:
  goal: "Add a dark mode option to the settings page so users can switch the settings experience between light and dark appearances and keep that preference after refresh."
  scope:
    in_scope:
      - "Add a settings-page control for choosing light or dark mode."
      - "Persist the selected appearance for the current user or browser session using the project's existing preference mechanism."
      - "Apply dark mode styling to the settings page and its existing controls."
      - "Add focused verification for the settings-page toggle and persistence behavior."
    out_of_scope:
      - "Redesign the full application theme system."
      - "Apply dark mode to unrelated pages unless required by shared settings-page components."
      - "Add new branding, color palettes, or accessibility features beyond contrast required for the settings page."
  success_criteria:
    - "Users can switch the settings page between light and dark appearances."
    - "The selected appearance remains active after refreshing or revisiting the settings page."
    - "Primary settings-page text, controls, and actions remain visible with sufficient contrast and stay operable in both appearances."
    - "The final changes stay limited to the settings-page dark mode behavior and required shared helpers."
  evidence:
    - criterion: "Toggle behavior"
      proof:
        - "Run the targeted UI or component test that exercises the settings-page appearance control."
        - "Manually verify the control changes the settings page from light to dark and back."
    - criterion: "Persistence"
      proof:
        - "Run or document a test that reloads the settings page after changing the appearance."
        - "Inspect the persistence mechanism used by the project for user or browser preferences."
    - criterion: "Legible and operable settings page"
      proof:
        - "Capture or inspect the settings page in both appearances."
        - "Confirm primary text, form controls, and actions have sufficient contrast."
    - criterion: "Scope control"
      proof:
        - "Inspect the final diff for unrelated theme redesign, branding changes, or broad page rewrites."
  guardrails:
    - "Escalate if the project has no existing preference mechanism and persistence requires a product decision."
    - "Escalate if applying dark mode to settings requires changing shared components used across many unrelated pages."
    - "Do not introduce a new global design system unless explicitly approved."
```

## Why This Works

The contract narrows a broad feature request into a specific settings-page change. It makes persistence and operability observable, ties each criterion to evidence, and blocks accidental expansion into a full theme redesign.

## Common Failure Mode

Avoid writing a contract that says only "add dark mode" with evidence like "looks good." That leaves a future agent guessing which page, how preference should persist, and how completion will be proven.

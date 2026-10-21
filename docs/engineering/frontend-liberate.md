## What it does

`frontend-liberate` applies Liberate's dark mode Tailwind CSS theme to web interfaces. The theme reference supplies `.dark` variables, semantic utility mappings, and base styles. Saans supplies the type hierarchy, with Arial as its fallback. White logo assets are included for surfaces carrying Liberate branding.

The agent uses the CSS reference to update the target project's own styles. It adapts the tokens to that project's Tailwind setup and keeps the requested UI dark regardless of system theme preference.

## When to reach for it

Type `/frontend-liberate`, or the [agent](https://www.aihero.dev/ai-coding-dictionary/agent) reaches for it automatically when a Tailwind CSS interface should follow Liberate's dark mode standard.

| Situation | What it applies |
| --- | --- |
| A new Tailwind page or component | The dark theme variables, semantic utilities, Saans hierarchy, and white Liberate logo assets where appropriate |
| An existing Tailwind interface being restyled | The reference values merged into its current theme and scoped to the requested surface |
| A Tailwind interface review | A check that the dark tokens render correctly, with usable typography, focus, and foreground contrast |

## The Tailwind theme

| Foundation | Standard |
| --- | --- |
| Theme scope | `.dark` variables for dark mode only, mapped to Tailwind utilities with `@theme inline` |
| Color examples | Black `#000000` background, green-tinted `#020C0A` cards, yellow `#FFF845` primary actions, and yellow `#FFE901` sidebar primary |
| Status colors | Pink `#FF637E` destructive, amber `#F59E0B` warning, and green `#22C55E` success; each uses black text |
| Other tokens | Semantic colors for popovers, sidebars, and charts, plus radius, shadow, tracking, and spacing values |
| Typeface | Saans Semibold for hero messages, Medium for supporting copy, Regular for body text; Arial fallback |
| Brand assets | White wordmark, symbol, and favicons for dark surfaces carrying Liberate branding |

The typography reference decides the sans font family even though the theme CSS contains a generic sans stack.

## Applying the reference

The color file is a reference for editing the target project's Tailwind stylesheet or configuration. The agent merges its dark variables and semantic mappings with what the project already uses, then updates the requested UI to use those utilities. Importing the file from the skill directory would leave the project's own styling system out of sync.

The agent applies `.dark` to the requested UI and keeps native controls dark. Existing layout conventions and components remain the starting point. Explicit user instructions override the relevant visual choices, and changes stay scoped to the requested surface.

## Common questions

**Do I copy or import the color file?**

No. The agent uses it as a reference and updates the project's own Tailwind theme. Existing imports, variants, and base rules are reused where possible.

**What happens when the system prefers light mode?**

The requested surface stays in dark mode. The white Liberate logo assets appear on dark backgrounds where that branding belongs.

**Does the project need Saans installed first?**

No. The agent uses Saans when available and Arial when it is not. Its sans font token follows the typography reference rather than the generic stack in the theme CSS.

**Does it work with a non-Tailwind styling system?**

This skill targets Tailwind CSS. Its theme directives and semantic utilities need a Tailwind setup; it does not define a separate stylesheet for another styling system.

## It's working if

- The target project contains the adapted `.dark` variables and semantic Tailwind mappings in its own styles.
- The requested interface stays dark at every viewport and system theme preference, including native controls.
- Components use the semantic theme utilities, with visible focus and readable foregrounds.
- Headlines, supporting copy, and body text follow the Saans hierarchy or use Arial when Saans is unavailable.
- White Liberate logo assets remain visible on dark backgrounds wherever the surface uses Liberate branding.
- The completion report names the rendered checks performed and any token pairings that needed adjustment.

## Where it fits

`frontend-liberate` is a standalone for Tailwind CSS implementation, styling, redesign, and review when the interface should follow Liberate. Use [prototype](https://aihero.dev/skills-prototype) to explore a specific UI question with runnable alternatives. [ask-matt](https://aihero.dev/skills-ask-matt) routes you through the wider skill set.

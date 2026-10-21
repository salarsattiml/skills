---
name: frontend-liberate
description: Apply Liberate's dark mode Tailwind CSS theme, Saans typography, and white logo assets. Use when building, styling, redesigning, or reviewing an interface that should follow Liberate in a Tailwind CSS project.
license: MIT
---

# Liberate Frontend

Apply Liberate's visual standard to the requested Tailwind CSS surface in dark mode. This skill covers dark mode only. Preserve the product's name, logo, content, and functionality unless the user asks to change them.

## Read the standard

Read both references before choosing visual values or reviewing their use:

- [Color](references/color.md) is a Tailwind CSS reference containing `.dark` variables, `@theme inline` mappings, status colors, and base styles. Use it to update the **target project's own styles**. Integrate its values into that project's Tailwind setup; the reference file is not a stylesheet to import from this skill.
- [Typography](references/typography.md) defines Saans, its weight hierarchy, and the Arial fallback. Its font choice takes precedence over the generic `--font-sans` stack in the color reference.

For surfaces carrying Liberate branding, inspect the white wordmark, symbol, and favicon files in [logo/white-logo](logo/white-logo/). Use these assets as supplied on dark backgrounds. Keep another product's existing logo when applying the visual standard to that product.

Use the references as the visual defaults. Carry forward explicit overrides from the user.

## Apply it

1. Inspect the target project's Tailwind setup, stylesheet entry point, dark mode selector, existing theme variables, shared components, and font assets. Identify which styles and surfaces the request covers.
2. Update the project's stylesheet or Tailwind configuration from the color reference. Merge its `.dark` values and semantic utility mappings with the existing setup. Reuse an existing Tailwind import, dark variant, or base layer where present. Adapt the reference's CSS directives to the project's Tailwind setup instead of duplicating global rules.
3. Keep the requested UI in dark mode regardless of system preference. Ensure the `.dark` variables apply to its root and set `color-scheme: dark` for native controls. Scope global token and base-style changes so they do not restyle unrelated surfaces. Define no light mode counterpart from this reference.
4. Apply the typography reference to the target project's sans font token and text hierarchy. Use Saans where available and Arial when it is unavailable. Update the requested UI to use the mapped semantic Tailwind utilities instead of its previous color values.
5. Use the supplied white logo assets where the surface carries Liberate branding. Choose the wordmark, symbol, or matching favicon size for its placement.
6. Implement or review the requested surface using its existing component and layout conventions. For implementation, cover responsive layout and relevant interaction states.
7. Inspect the rendered result. Confirm the dark variables and semantic utilities are active, the appearance stays dark under either system preference, and typography, logos, focus, and relevant states remain usable. Check foreground contrast, especially on primary, destructive, warning, success, and sidebar actions. If a reference pairing needs adjustment, change the target project's foreground token and report it.

Keep changes scoped to the requested surface unless the user asks for a broader redesign. Report what changed in the target project, what was checked, and any remaining issues.

# Chart Builder 0.1.5 for apexcharts-card

Date: 12 October 2026
Works with: apexcharts-card 2.3.1-apex8 and 2.3.0-apex8, the builds with ApexCharts 8.0.0 (see `apexcharts-card-apex8.en.md`)

The builder's interface is in Dutch. Where this document names a button, tab or field, the Dutch label is given as it appears on screen, with the English meaning in parentheses.

## What it is

A dedicated page in the Home Assistant sidebar (custom panel) where you build an apexcharts-card visually:

- on the left, the form with all card options, grouped in tabs;
- on the right, a live preview with your real data, in your own theme;
- below that, the YAML, with a copy button.

No add-on and no container: it is a single JS file that Home Assistant loads. The panel uses the same connection as your dashboard, so no token and no separate login.

## Installation

1. Make sure apexcharts-card 2.3.1-apex8 is loaded as a dashboard resource. The builder uses that card for the preview.
2. Put `apexcharts-builder.js` in `/config/www/`.
3. Add to `configuration.yaml`:

```yaml
panel_custom:
  - name: apexcharts-builder
    sidebar_title: Grafiekbouwer
    sidebar_icon: mdi:chart-line
    url_path: grafiekbouwer
    module_url: /local/apexcharts-builder.js?v=0.1.5
    require_admin: true
```

4. Check the configuration and restart Home Assistant. For a new version: replace the file, raise `?v=` in `configuration.yaml` and restart again, because `panel_custom` is only read at startup. Replacing only the file also works, but browsers and the companion app may then keep loading the old version from cache.
5. "Grafiekbouwer" now appears in the sidebar, for administrators only. Change `sidebar_title` if you want another name.

Files in `/local/` are served without authentication. The builder file contains nothing secret.

## Usage

1. Pick an entity and a template: Standaard lijn (standard line), 24 uur trend (24-hour trend), Dagteller per dag (daily counter per day), Statistics 60 dagen (statistics, 60 days) or Leeg (empty).
2. Adjust in the tabs:
   - **Algemeen (General):** chart type, span, alignment, offset, stacked, refresh, colour list.
   - **Series:** per series the basics, line and area, fetching and grouping data (group_by or statistics), display and threshold colours. Series can be reordered, duplicated and removed.
   - **Y-assen (Y axes):** min and max (also `~90` and `|+10|`), decimals, left or right, number of steps, axis title. When you add a second axis, existing series are linked to the first axis automatically.
   - **Kop en tijd (Header and time):** header, title, current values, now line.
   - **Opmaak (Styling):** the most used ApexCharts options (height, fill, column width, grid, x-axis format, tooltip, legend, thinning, colour-blind palette) plus the full `apex_config` as YAML.
   - **Extra:** experimental options and brush.
3. Click **Kopieer YAML** (Copy YAML). Paste it into a dashboard under Add card, Manual, or into the code editor of an existing card.

To edit an existing card: paste its YAML into the YAML box. The form takes over everything. Whatever the builder does not know (`card_mod`, `EVAL` functions, `config_templates`, ...) is left untouched.

Every option shows its default value. An explanation sits behind the small (i) next to the label: click it to open; click again, click the cross, click elsewhere or press Escape to close it. A two-state option has three positions: **std** (default, not in the YAML), **aan** (on) and **uit** (off). Only what you set yourself ends up in the YAML.

The entity search also matches area and label names, as they are named in your Home Assistant. Two filters below the search field narrow by area (grouped by floor) and by label. An entity without its own area or label inherits those from its device. The filters apply to all search fields at once.

## What the builder checks for you

Messages appear above the form, where possible with a button that fixes the issue right away:

- threshold colours without the experimental switch (button: turn it on);
- "start with last value" together with a maximum, a common pitfall with daily counters (button: turn it off);
- statistics and grouping at the same time (a note only: delta per day on hourly statistics is valid);
- hidden by default or in brush without the matching experimental option (when you turn these on in the form, the builder sets the switch itself; the message only appears for pasted YAML);
- multiple y axes without an ID, or series without a chosen axis;
- shared tooltip with series that are grouped differently;
- stack group without "Stacked", data labels on raw data, radialBar without max.

The checks take `all_series_config` and series from a `data_generator` into account.

The card itself also validates every change. If it rejects a config, the reason appears above the preview. Invalid duration values (span, grouping, offset) are blocked as you type them.

Undo remembers the last 60 steps. Work in progress is kept in the browser, even if you briefly switch to a dashboard.

## Changelog

### 0.1.5

- Turning on "Standaard verborgen" (hidden by default) for a series now also sets `experimental.hidden_by_default: true`. Without that switch apexcharts-card silently ignores the option.
- The same for "In brush": it sets `experimental.brush: true`.
- Both now behave like threshold colours, which already did this. The message with the "Aanzetten" (turn on) button remains for pasted YAML.
- The message about threshold colours referred to a tab "Experimenteel" that does not exist; it now says "Extra".
- Dependencies updated (lit 3.3.3, js-yaml 4.3.2), which makes the file slightly larger.
- Tested: only the new logic, in isolation in Node.

### 0.1.4

- An explanation for every option (116 fields), behind a small (i) next to the label. Only a click opens it; there are no hover tooltips, so it also works on a phone.
- Explanations are no longer shown under the fields by default, which makes the form calmer.

### 0.1.3

- The versions of the builder and of the loaded apexcharts-card are shown in the header bar (on a phone below the title). The card version comes from the card itself, not from the `?v=` of the resource.
- The preview box takes the height of the card; the YAML box fills the rest. Scrolling is only needed when the card does not fit.
- The YAML box can be collapsed (click "YAML"), which gives the preview all the space. The builder remembers that choice in the browser.

### 0.1.2

- Entity search on area and label, with area and label filters.
- The results show area and labels for every entity; a selected entity shows its area underneath.

### 0.1.1

- The checks take `all_series_config` and `data_generator` series into account, which removed false warnings about the shared tooltip.
- "Statistics and grouping at the same time" is a note without a button.
- Tested with 80 real-world cards (44 of them using `all_series_config`): all 80 pass through the builder without loss.

## How it was tested

What was tested separately for a later version is listed with that version (0.1.1 and 0.1.5). The base test ran in headless Chromium with a simulated `hass` and the real apexcharts-card 2.3.0-apex8 as preview:

- all five templates, dark and light theme, desktop and phone width (390 px, no horizontal scrolling);
- five real-world apexcharts configs pasted in and read back out: all five without loss, including `EVAL` functions and a `data_generator` as a block string;
- changing one field in an imported config changes only that field;
- threshold colours turn on the experimental option, y axes link series, undo works, broken YAML and invalid options give a readable message;
- no console errors.

**Not simulated:** loading as a panel, the sidebar button on a phone and the Home Assistant theme colours. Those only show in a real instance; try a new version on a test instance first if you have one.

## Known limitations

- Made for apexcharts-card 2.3.x-apex8. The options under "ApexCharts 8" (Opmaak tab) do not exist in upstream 2.2.3 or older.
- The interface is Dutch only.
- Click actions (`tap_action` and the like) and `all_series_config` can only be edited as YAML.
- The entity search is custom code and does not use Home Assistant components. Areas and labels come from the registries Home Assistant hands to every page, plus one request for the label names. That keeps it from breaking on Home Assistant updates, but it looks slightly different from the standard picker.
- On a low screen a tall card may still not fit; collapse the YAML in that case.

## Files

- `apexcharts-builder.js`: the panel (140 kB), SHA-256 `d209ae4e0a444b7d497d9421db630bc392d6d646e804c69d8b92f70c69f7900e`
- `grafiekbouwer-bron/`: the source code (Lit and js-yaml; build with `npm install` and `npx rollup -c`)
- `grafiekbouwer-voorbeeld.png`: screenshot

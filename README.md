# COMO P-1-H Scannable ID QR Codes

A Tampermonkey userscript for the COMO Operations Dashboard that finds package rows whose **Last Known Location** begins with `P-1-H` and generates a scannable QR code from each row's **Scannable ID**.

Instead of opening a new browser tab, the script displays a **centered QR scanner popup directly over the current dashboard**. The dashboard URL does not change.

> **Important:** The QR encodes the **Scannable ID**, never the Last Known Location.
>
> | Last Known Location | Scannable ID | QR encodes |
> |---|---|---|
> | `P-1-H209A190` | `PHQLR2XLWJGTG5WKDGBC` | `PHQLR2XLWJGTG5WKDGBC` |

## Features

- **In-page QR popup:** Opens in the center of the existing COMO Dashboard tab, with a close (`×`) button. No new tab or browser navigation is required.
- **Single-code scanner:** Displays one large QR code at a time, with the corresponding Scannable ID below it.
- **QR count:** Displays the current position and total, such as **QR 1 OF 4 TO PRINT**. The launcher also displays the number of matching QR codes found.
- **Left/right navigation:** Use the on-screen arrows or keyboard **Left** and **Right** keys. Navigation stops at the first and last code; it does **not** wrap around.
- **Automatic popup sizing:** Adjusts the popup when browser zoom or window dimensions change. The QR, text, and arrows stay together within the popup layout.
- **Manager Actions integration:** Places the **P-1-H QR Codes** button next to **Complete Task**, instead of using a draggable or floating button.
- **Unavailable-state handling:** Disables the button when no matching QR data is available.
- **Duplicate suppression:** Identical **location + Scannable ID** pairs are shown only once.
- **Local QR generation:** Includes its own QR encoder; no QR-generation CDN is required.
- **Reduced background work (v1.7.3):** Avoids unnecessary button remounting, throttles relevant table scans, and limits repeated work during dashboard updates. The routine “QR codes ready” notification is disabled; error messages can still appear.

## Install

1. Install [Tampermonkey for Chrome](https://chromewebstore.google.com/detail/tampermonkey/dhdgffkkebhmkfjojejmpbldmpobfkfo), [Edge](https://microsoftedge.microsoft.com/addons/detail/tampermonkey/iikmkjmpaadaobahmlepeloendndfphd), or [Firefox](https://addons.mozilla.org/firefox/addon/tampermonkey/).
2. [Install the script](https://raw.githubusercontent.com/IbrahimaSy11/como-p1h-qr-codes/main/como-p1h-qr-codes.user.js) and confirm installation in Tampermonkey.
3. If replacing an older version installed manually, make sure only one version is enabled to avoid duplicate buttons or conflicting behavior.

The userscript includes Tampermonkey update/download URLs pointing to the GitHub `main` branch. **Automatic updates depend on that published file being updated**; installing a locally downloaded version does not by itself update the GitHub repository.

> The script operates on specified pages of the internal COMO Operations Dashboard. You must have access to that dashboard.

## Usage

1. Open a cart/task detail page containing the **Last Known Location** and **Scannable ID** table columns.
2. Look in **Manager Actions**, next to **Complete Task**, for **P-1-H QR Codes** and its QR count.
3. Click **P-1-H QR Codes**. The QR popup opens over the dashboard without changing the browser address.
4. Scan the QR code. Use the **left/right arrows** (or keyboard **← / →**) to move between codes.
5. Read the Scannable ID underneath the QR to verify the current item. The counter indicates your position in the set.
6. Click **×** to close the popup and return to the same dashboard view.

The popup presents QR codes for scanning. **This version does not provide the previous printable grid/new-tab Print workflow.**

## Configuration

Edit these settings near the top of the userscript in Tampermonkey:

| Setting | Default | Purpose |
|---|---|---|
| `TARGET_PREFIX` | `'P-1-H'` | Only match locations starting with this prefix, case-insensitively. |
| `ECC_LEVEL` | `'M'` | QR error correction level (`L`, `M`, `Q`, or `H`). |
| `MODULE_PX` | `8` | QR SVG's natural module sizing. The popup has its own display styling. |
| `QUIET_ZONE` | `4` | White border around the QR, in modules; keep at least four for reliable scanning. |

## Performance and behavior

Version **1.7.3** reduces repeated DOM work by responding primarily to relevant table and Manager Actions changes. It also throttles QR-count scans during rapid table updates, limits table-data retry attempts when opening the scanner, and uses a low-frequency URL check as a navigation safety net. QR generation runs locally in the browser.

These changes are intended to reduce script-induced lag, but actual performance can depend on dashboard activity, table size, and other enabled userscripts.

## Troubleshooting

**P-1-H button is missing**

Open a supported cart/task detail view and wait for the Manager Actions controls to load. The script attempts to position its button directly after **Complete Task**. Check that Tampermonkey is enabled and that duplicate older versions are disabled.

**P-1-H button shows 0 or is disabled**

No eligible `P-1-H` rows were detected in the currently readable table. Check whether the table is fully loaded and whether the location values begin with the configured prefix.

**No QR codes found / item table not ready**

The script searches for an HTML table whose column headers include **Last Known Location** and **Scannable ID**. If the table has not loaded or the page uses different markup, generation may fail. The script retries briefly before reporting an error.

**Some expected packages are missing**

Only rows currently present in the page's readable table can be processed. Pagination or virtualization may hide rows from the script. Duplicate location/Scannable ID pairs are intentionally collapsed.

**Popup is too large or small when zooming**

The popup compensates for browser zoom and also attempts to fit the visible viewport. At extreme zoom or in small windows, viewport limits can prevent further enlargement. This version uses automatic sizing, not a fixed-size popup.

**Buttons flicker or the site slows down**

Disable earlier versions of this userscript and check for conflicting dashboard userscripts. Version 1.7.3 reduces repeated launcher placement and table scanning, but cannot guarantee lag-free performance on every dashboard page.

**Popup blocked**

This version opens an **in-page overlay**, not a browser popup window. Browser popup-blocker permissions normally are not needed for the scanner.

## About the QR encoder

The QR encoder is bundled directly with the script because the internal dashboard may not be able to reach external CDNs. It uses QR byte mode and supports versions 1–10.

The original encoder's release notes report bit-for-bit comparisons with the [`qrcode`](https://www.npmjs.com/package/qrcode) reference implementation and successful decoding of 3,000+ generated codes with [`jsQR`](https://www.npmjs.com/package/jsqr). These are **historical project validation claims**, not a new independent test performed for this README update.

## License

MIT — see [LICENSE](LICENSE).

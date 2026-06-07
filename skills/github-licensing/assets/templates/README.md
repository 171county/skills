# License & Supporting-File Templates

Drop-in templates for the **Implement** step of the `github-licensing` skill.

## How to use these

1. **Short permissive licenses** (`MIT.txt`, `BSD-2-Clause.txt`, `BSD-3-Clause.txt`, `ISC.txt`) are reproduced here in full and verbatim. Copy to the repo as `LICENSE`, then fill in `[year]` and `[fullname]`.
2. **Long license texts are now bundled here, verbatim and unmodified** — copy them straight to `LICENSE`, no editing. These are the exact canonical SPDX texts:

   | License | SPDX ID | Bundled file | Notes |
   |---|---|---|---|
   | Apache-2.0 | `Apache-2.0` | `Apache-2.0.txt` | append the appendix header to source files; ship a `NOTICE` if you carry attributions |
   | GPL-2.0 | `GPL-2.0-only` / `-or-later` | `GPL-2.0.txt` | |
   | GPL-3.0 | `GPL-3.0-only` / `-or-later` | `GPL-3.0.txt` | |
   | AGPL-3.0 | `AGPL-3.0-only` / `-or-later` | `AGPL-3.0.txt` | the anti-SaaS-freeloader copyleft; see `../references/copyleft.md` §13 |
   | LGPL-2.1 | `LGPL-2.1-only` / `-or-later` | `LGPL-2.1.txt` | |
   | LGPL-3.0 | `LGPL-3.0-only` / `-or-later` | `LGPL-3.0.txt` | LGPL-3.0 is a supplement to GPL-3.0 — also ship `GPL-3.0.txt` as `COPYING` |
   | MPL-2.0 | `MPL-2.0` | `MPL-2.0.txt` | file-level copyleft |
   | EPL-2.0 | `EPL-2.0` | `EPL-2.0.txt` | |

   ⚠️ **Never retype these from memory or modify the body.** If you ever need to confirm the bytes are current, the authoritative mirror for any SPDX ID is `https://spdx.org/licenses/<SPDX-ID>.txt` (e.g. `GPL-3.0-only`). GNU originals: gnu.org/licenses; Apache: apache.org/licenses/LICENSE-2.0.txt; MPL: mozilla.org/MPL/2.0; EPL: eclipse.org/legal.

3. **Not bundled — fetch live, do not redistribute a stale copy** (terms/versioning change and some restrict modification): Elastic-2.0 (`https://www.elastic.co/licensing/elastic-license/elastic-license-2.0-text`), SSPL-1.0 (`https://www.mongodb.com/legal/licensing/server-side-public-license`), PolyForm family (`https://polyformproject.org/licenses/`).

4. **Parameter-driven source-available licenses:** `BSL-1.1.txt` and `FSL-1.1.txt` are fill-in templates — complete the parameter block, don't touch the body.
5. **Supporting files:** `DCO.txt`, `NOTICE.template`, `TRADEMARK.md.template`, `CONTRIBUTING.snippet.md`, `spdx-headers.md`, `dual-license-header.txt`.

## Always confirm before writing
- **Copyright holder** (legal name / entity) and **year** (year of first publication; a single year is fine).
- The exact **SPDX identifier**, including the `-only` / `-or-later` choice for GNU licenses.
- For BSL/FSL: the Licensor, Licensed Work, Additional Use Grant / Competing Use, Change Date, Change License.

## Files GitHub's `licensee` recognizes
`LICENSE`, `LICENSE.txt`, `LICENSE.md`, `COPYING`. Keep the canonical text unmodified so the badge resolves. Modified text → declare with a `LicenseRef-` identifier (see `../references/protection.md`).

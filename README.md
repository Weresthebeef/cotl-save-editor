# Cult of the Lamb Save Editor

A browser-based editor for inspecting and modifying **Cult of the Lamb** save data as JSON. Open a save, edit its contents, and download a new copy in plain JSON or the supported encrypted save format.

This repository is [Kurariahunter's fork](https://github.com/Kurariahunter/cotl-save-editor) of [Khaomi/cotl-save-editor](https://github.com/Khaomi/cotl-save-editor). It retains the original project's code and ISC license; the license credits **xhayper**.

## Features

- Open a save with the file picker or drag and drop it onto the editor.
- Read plain JSON saves and the implemented AES-CBC save wrapper.
- Inspect and edit save data using the embedded JSON editor.
- Choose encrypted or plain JSON output with **Encrypt save file**.
- Set the download filename; importing a file fills in its original name.
- Switch between light and dark themes.
- Process save imports and exports in the browser using FileReader, Web Crypto, and downloadable blobs. The reviewed source has no save-upload endpoint.

The editor exposes raw save data. It does not provide dedicated buttons for granting resources, changing followers, or unlocking progression, and it does not validate whether an edited value is meaningful to the game.

## Using the editor

1. Close the game and make a separate backup of the save you want to edit.
2. Run the application locally using the instructions below, then open its URL in your browser.
3. Click the **folder icon** or drag a save file onto the page.
4. Confirm that the displayed JSON belongs to the save you selected before making changes.
5. Edit the desired values while preserving the surrounding structure and value types.
6. Check **Encrypt save file** and **File name**. A successful import selects the output mode to match the imported format; keep that mode unless you intentionally want to convert it.
7. Click the **download icon** to save the edited copy.
8. With the game still closed, place the edited file in the appropriate save location for your installation. Keep the untouched backup until you have verified the result in-game.

The application downloads a new file; it does not automatically replace your installed save or create a backup. Check for a browser-added suffix such as `(1)` before copying the download into your save folder.

### Encrypted and plain output

The current implementation recognizes an encrypted save by its leading `E` byte. It reads an embedded 16-byte key and 16-byte IV, then decrypts the remaining payload with AES-CBC before parsing the JSON. Encrypted export creates a new key and IV and writes the same wrapper structure.

Unchecked, **Encrypt save file** exports plain JSON. The filename alone does not determine whether the contents are encrypted; the initial suggested filename is `slot_0.json`.

Because the key is included in this save wrapper, this encryption is a game-format requirement, not a way to keep the save's contents secret.

## Run locally

### Requirements

- Node.js compatible with this project's Nuxt dependencies.
- **pnpm 8.6.11**, the package-manager version recorded in `package.json`.
- A browser with Web Crypto support. Use `localhost` for local development or HTTPS when hosting the editor.

The checked-in dependency set uses Nuxt 3.6.x and a pnpm v6-format lockfile. It is an older dependency snapshot; this README does not claim that every current Node.js version has been tested against it.

```bash
git clone https://github.com/Kurariahunter/cotl-save-editor.git
cd cotl-save-editor
pnpm install --frozen-lockfile
pnpm dev
```

Open the URL printed by the development server, normally `http://localhost:3000`.

### Build commands

| Command | Purpose |
| --- | --- |
| `pnpm dev` | Start the development server. |
| `pnpm build` | Run Nuxt type checking, then create a production build. |
| `pnpm preview` | Preview a production build locally. |
| `pnpm generate` | Run type checking, then generate static output. |

For a local production preview:

```bash
pnpm build
pnpm preview
```

The `postinstall` script runs `nuxi prepare`. For static hosting, `pnpm generate` produces the Nuxt site's output under `.output/public`; host it over HTTPS rather than opening the files directly with `file://`.

## Compatibility and known limitations

- **No verified game-version or platform matrix is included.** Supporting the wrapper and JSON does not establish compatibility with every game update, DLC, or console save container.
- **Import errors are currently silent.** An unsupported, damaged, or incorrectly decrypted file may leave the previous data displayed. Do not assume a new file loaded just because its filename changed.
- **Encryption errors are also caught silently.** If encrypted export fails, the implementation can return plain JSON even with the checkbox selected. Check browser Web Crypto availability and the exported format when troubleshooting.
- **JSON editing is not game-schema validation.** Valid JSON can still contain missing fields, invalid values, or inconsistent progression state.
- There is no automatic backup, direct save-folder integration, or cloud-save synchronization management.
- No automated test command is defined in `package.json`. The existing build scripts perform type checking, which is separate from validating edited saves in the game.

## Source layout

| File | Responsibility |
| --- | --- |
| `pages/index.vue` | File picker, drag and drop, filename/encryption controls, JSON editor binding, and download action. |
| `composables/saveState.ts` | Save import, JSON parsing, AES-CBC wrapper handling, and export. |
| `components/JsonEditorVue.client.vue` | Browser-only wrapper around `json-editor-vue`. |
| `composables/useTheme.ts` | Light/dark theme state, stored locally under the `theme` key. |
| `components/ThemeLoader.vue` | Theme initialization. |
| `nuxt.config.ts` | Nuxt configuration, modules, styling, and TypeScript options. |
| `package.json` / `pnpm-lock.yaml` | Scripts, dependencies, package-manager version, and dependency lockfile. |

The UI is built with Nuxt, Vue, TypeScript, Windi CSS, and `json-editor-vue` / `vanilla-jsoneditor`.

## Contributing

Useful improvements include visible import/export errors, save-format validation, documented game-version compatibility, and tests for plain/encrypted round trips. When reporting a problem, include the game version, platform, whether the input is encrypted, and any browser-console error. Avoid posting a complete personal save unless you intend to share its contents.

## Credits and license

- Original project: [Khaomi/cotl-save-editor](https://github.com/Khaomi/cotl-save-editor).
- Existing license copyright: **2023 xhayper**.
- This fork: [Kurariahunter](https://github.com/Kurariahunter).
- JSON editor: [json-editor-vue](https://github.com/cloydlau/json-editor-vue) and [vanilla-jsoneditor](https://github.com/josdejong/svelte-jsoneditor).
- Cult of the Lamb and its assets belong to their respective owners. This is an unofficial community tool.

Released under the [ISC License](LICENSE). Preserve its copyright and permission notice when redistributing the software.

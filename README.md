# Burp Scope Monitor Extension — Custom Fork

A Burp Suite Extension to monitor and keep track of tested endpoints.

![](http://g.recordit.co/MwAyLS1VkZ.gif)

## Credits

This project is a fork of the original **Burp Scope Monitor** extension developed by **[@Regala](https://github.com/Regala)**.

- Original repository: [https://github.com/Regala/burp-scope-monitor](https://github.com/Regala/burp-scope-monitor)

All original features, logic and structure were preserved. The changes described in the section below were layered on top of the original work.

---

## Improvements over the original

### Tags column
- A **Tags** column was added to the endpoints table, allowing each endpoint to be labelled with one or more tags (e.g. `Login`, `MFA`, `PII`).
- Tags are fully editable inline: double-click any cell in the Tags column to type directly.
- Multiple tags are stored and displayed with a `|` separator (e.g. `Login|MFA`).
- Tags are persisted in the CSV state file on export/import.

### Tags context menus
- A **Tags submenu** was added to the global Burp context menu (right-click anywhere in Proxy History, Repeater, etc.), making it possible to tag an endpoint without switching to the extension tab.
- A **Tags submenu** was also added to the extension's own right-click popup menu inside the Monitor tab.
- Both menus support multi-selection: selecting multiple rows and clicking a tag applies it to all of them.
- Tags accumulate — clicking `MFA` on a row already tagged `Login` results in `Login|MFA`, not a replacement.

### Centralised tag list
- All available tags are defined in a single constant at the top of `main.py`:
  ```python
  TAGS = ['Login', 'Logout', 'MFA', 'PII']
  ```
  Adding, removing or renaming a tag only requires editing this one line — both context menus are generated dynamically from it.

### Bug fixes
- Right-click now correctly selects the row under the cursor before showing the popup, ensuring the tag is always applied to the intended endpoint.
- Table refresh calls (`fireTableDataChanged`) are now dispatched on the Swing EDT via `SwingUtilities.invokeLater`, preventing race conditions when the Burp callback fires from a background thread.

---

## Main Features

- Simple, easy way to keep track of unique endpoints when testing an application
- Mark individual endpoints as analyzed or not
- Instantly understand when a new endpoint, not tested is requested
- Accessible from Proxy tab (right click, mark request as analyzed/not)
- Tag endpoints with custom labels from any context menu
- Send to Repeater
- Enforcement of Burp's in scope rules
- Import/Export state file directly to a CSV file
- Autosave option

## Installation

1. Make sure you have Jython configured under Extender -> Options -> Python Environment. For further instructions, check PortSwigger official instructions at their [support page](https://support.portswigger.net/customer/portal/articles/1965930-how-to-install-an-extension-in-burp-suite).
2. Clone or download this repository.
3. Import [main.py](main.py) in Extender — Extender -> Extensions -> Add -> Select Python -> Select [main.py](main.py)

## Documentation

Most of the options available in General or Import tabs are auto-explanatory. 

- *"Repeater request automatically marks as analyzed"* - when issuing a request to an endpoint from repeater, it marks this request as analyzed automatically.
- *"Color request in Proxy tab"* - this essentially applies the behavior of the extension in the Proxy tab, if you combine these options with "Show only highlighted items" in Proxy. However, it's not as pleasant to the eyes as the color pallete is limited. 
- *"Autosave periodically"* - backups the state file every 10 minutes. When activating this option, consider disabling *"Autostart Scope Monitor"*. This is in order to maintain a different state file per Burp project. However, you can easily maintain only one, master state file.
- *"Import/Export"* is dedicated to handle the saved state files. It's preferred to open your Burp project file associated with the Scope Monitor. It will still work if the Burp project is different, but when loading the saved entries, you won't be able to send them to Repeater or view the request itself in the Request/Response viewer (this is due to the fact that we are not storing the actually requests - just the endpoint, it's analyzed status and a couple of other more. This makes it a little bit more efficient).

## Future Development

- Keep track of parameters observed in all requests
- Highlight when a new parameter was used in an already observed/analyzed endpoint
- Export to spreadsheet / Google Sheets
- Adding notes to the endpoint

## Implementation

The code is not yet performant, optimized or anything similar. KISS and it works. Performance will be increased depending on demand and how the extension performs when handling large Burp projects.

To circumvent some of Burp's Extender API limitations, some small hacks were implemented. One of those is automatically setting a comment on the requests that flow in the Proxy tab. You can still add comments on the items, as you'd normally would, but just make sure to keep the placeholder string (`scope-monitor-placeholder`) there. Hopefully in the future each requestResponse from Burp will have a unique identifier, which would make the import state / load from file much cleaner and fast. With large state files, this might hang a bit when loading.

## Contributing

I welcome contributions from the public, from bug reports, feature suggestions and pull requests.

### Using the issue tracker 💡

The issue tracker is the preferred channel for bug reports and features requests.

### Issues and labels 🏷

The bug tracker utilizes several labels to help organize and identify issues.

### Guidelines for bug reports 🐛

Use the GitHub issue search — check if the issue has already been reported.

### Known bugs:

- Sometimes when switching from "Show All" to "Show New Only" Burp hangs/crashes. If you encounter this behavior please let me know how you reproduce it. 
- Manually marking requests as analyzed from the main extension UI tab doesn't apply colors in the proxy
- The import/export function often makes Burp freeze (it unfreezes after a while) so this needs a review, probably has something to do with the locks

## Special Thanks

- BlazeIt team
- BBAC
- HackerOne

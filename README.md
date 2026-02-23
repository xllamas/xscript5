# xScript

A lightweight JavaScript UI framework for building dynamic web interfaces using a clean object-oriented API. xScript provides a consistent, chainable interface for creating and managing DOM elements programmatically — no HTML templates required.

The framework is split into two files:

- **`xscript.js`** — Core base classes and plain UI components (no CSS framework dependency)
- **`xscript_bs5.js`** — Bootstrap 5 UI components that extend the core classes

Licensed under **GPLv3**.

---

## Getting Started

### Without Bootstrap

Include `xscript.js` in your HTML:

```html
<script src="xscript.js"></script>
```

### With Bootstrap 5

Include Bootstrap 5 first, then both xScript files:

```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
<script src="xscript.js"></script>
<script src="xscript_bs5.js"></script>
```

### Basic Usage

```javascript
// Create a section and append it to the document body
var panel = new xSection("my-panel");
panel.append(document.body);

// Add a button that shows an alert
var btn = new xButton("Click Me");
btn.bindFunction(function() { alert("Hello!"); });
panel.addElement(btn);
```

---

## Core Concepts

All xScript objects wrap a DOM node and expose a consistent chainable API. Every element has an auto-generated unique ID, a CSS class, and a reference to its parent container. The two foundational base classes are:

**`xElement`** — Abstract base for single-node elements. Provides `append()`, `show()`, `hide()`, `remove()`, `bindToEvent()`, `setClass()`, `setId()`, and more.

**`xMultiElement`** — Extends `xElement` for container elements that hold child elements. Adds `addElement()`, `removeElement()`, `getElement()`, `getNumElements()`, and `clear()`.

Most UI classes extend one of these two bases. All methods return `this` to support chaining.

---

## `xscript.js` — Core Classes

### Layout & Structure

**`xSection(clss)`** — A `<div>` container that holds child elements. The workhorse container class.

**`xPanel(clss)`** — Alias for `xSection`, semantically used as a generic panel.

**`xTitlePanel(title, bodyclss, titleclss, clss)`** — A panel with a title bar div and a body div. Use `addElement()` to add children to the body and `setTitle()` to update the heading.

**`xLayout(rows, cols, widths, clss)`** — A table-based grid layout. Place elements at specific row/column positions using `addElement(element, row, col)`. Optionally pass an array of column width strings.

**`xTwoElementSection(clss)`** — Abstract base for compound elements that render as two sibling DOM nodes.

**`xList(clss)`** — A `<ul>` that wraps each added element in a `<li>`.

**`xPopUp(clss)`** — A fixed-position overlay panel. Appends itself to `document.body` and centers automatically on `show()`. Control positioning with `setPos(x, y)` and `setSize(w, h)`.

**`xVerticalSpacer(px)`** — A blank `<div>` of the given pixel height, useful for spacing.

### Text & Content

**`xTextBox(text, clss)`** — A `<div>` for displaying HTML content. Supports `setText()`, `addText()`, and `clear()`.

**`xPara(text, clss)`** — A `<p>` element with `setText()` and `addText()`.

**`xSpan(text, clss)`** — A `<span>` with `setText()` and `addText()`.

**`xText(text)`** — A raw DOM text node.

**`xHtml(type, innerHtml, clss)`** — A generic element of any HTML tag type with inner HTML content. Use `addHtml()` to append additional markup.

**`xMultiHtml(type, clss)`** — Like `xHtml` but extends `xMultiElement` so child xScript objects can be added to it.

### Interactive Elements

**`xButton(label, clss)`** — A `<button>` element. Bind click handlers with `bindFunction(f)`. Supports `disabled(bool)`, `setLabel()`, and `setTooltip()`.

**`xLink(label, href, clss)`** — An `<a>` element with `bindFunction()`, `setTarget()`, and `disabled()`.

**`xImage(src, alt, clss)`** — An `<img>` element.

**`xCanvas(w, h, clss)`** — A `<canvas>` element. Access the canvas node with `getCanvas()` and a 2D drawing context with `getContext2d()`.

### Form Inputs

All input classes share the `xInput` base which provides `getValue()`, `setValue()`, `setPlaceholder()`, `setReadOnly()`, `setDisabled()`, `setName()`, `getName()`, and validator support via `addValidator()` and `validate()`.

| Class | Type | Description |
|---|---|---|
| `xStringInput(label, name)` | `text` | Labeled text input |
| `xNumberInput(label, name)` | `number` | Numeric input |
| `xEmailInput(label, name)` | `email` | Email input |
| `xDateInput(label, name)` | `date` | Date picker input |
| `xRangeInput(label, name)` | `range` | Slider input |
| `xHiddenInput(name, value)` | `hidden` | Hidden field |
| `xTextAreaInput(label, name, rows, cols)` | `textarea` | Multi-line text area |
| `xCheckBox(label, name)` | `checkbox` | Checkbox with `getValue()` returning a boolean and `setChecked(bool)` |
| `xRadioBtn(label, name)` | `radio` | Radio button |
| `xRadioBtnGrp(clss)` | container | Groups radio buttons; use `getChecked()` to get the 1-based index of the selected button |
| `xSelect(label, name)` | `select` | Dropdown with `addOption(label, val)`, `addOptions(arr)`, `setSelected(val)`, `clearOptions()` |

### Tables

**`xTable(clss)`** — An HTML table with `addHeadings(arr)`, `addRow(arr, clss)`, and `addFooter(arr)`. Row cells contain raw HTML strings.

**`xElementsTable(clss)`** — Like `xTable` but each cell holds an xScript element. Headings are also xScript elements. Retrieve any cell's element with `getElement(row, col)`.

### Validators

Validators attach to input elements via `input.addValidator(validator)` and are checked by calling `input.validate()`.

**`xValidator`** — Abstract base. Extend and override `validate()`.

**`xNotEmptyValidator`** — Fails if the input value is an empty string.

**`xNotZeroValidator`** — Fails if the input value is `0`.

**`xTelValidator(minDigits, maxDigits)`** — Validates phone numbers: digits and `+` only, within the specified length range. Empty values pass.

### Backend Interfaces

xScript includes interfaces for communicating with a backend (or an Android WebView host via a native `Android` object). These fall back to synchronous XHR calls when running in a standard browser.

**`xInterface`** — Abstract base. Provides `getRemoteInterface(query)`, `postRemoteInterface(query, data)`, `rawRemoteInterface(query)`, and `rawPostRemoteInterface(query, data)`.

**`xSQL`** — SQL database interface. Methods: `openDB(path)`, `sqlExec(q)`, `sqlInsert(q)` (returns row ID), `sqlSelect(q)` (returns cursor), `getNextRow()`, `getPrevRow()`, `getRowCount()`, `gotoLastRow()`, `closeCursor()`, `closeDB()`, `deleteDB(path)`.

**`xFile`** — File I/O interface. Methods: `open(path)`, `read()`, `write(data, append)`, `close()`, `deleteFile()`, `listFiles(path)`, `mkDir(path)`, `getExtStoragePath()`.

**`xExportFile`** — Stores data server-side and triggers a file download via form POST. Use `set(mime, filename, data)` then `get()`.

**`xExportProject` / `xImportProject`** — Project-level export and import helpers.

**`xHTTP`** — Simple HTTP client. Use `get(url)` and `post(url, data)`.

### Utility Functions

**`xAlertDialog(title, msg)`** — Shows a native Android alert dialog or a browser `alert()`.

**`xRunUserApp(title, app, debug)`** — Launches a sub-application URL in an Android WebView or a new browser tab.

**`xCloseApp()`** — Closes the Android activity or the browser window.

**`xGetURLParameter(name)`** — Parses and returns a query string parameter by name.

**`xGetLogCat(level)` / `xGetLogCatFormated(clss, level)` / `xClearLogCat()`** — Android logcat helpers (no-op in browser).

**`Number.prototype.formatMoney(decimals, decimalSep, thousandSep)`** — Formats a number as a currency string.

**`xMulti(clss)`** — A virtual container that groups multiple xScript elements without creating a wrapper DOM node. Used for managing sets of sibling elements that belong to the same parent.

---

## `xscript_bs5.js` — Bootstrap 5 Classes

These classes mirror their core counterparts but apply Bootstrap 5 utility classes automatically. They are prefixed with `xb`. All the same methods are available unless noted otherwise.

### Form Inputs

| Class | Bootstrap Classes Applied | Notes |
|---|---|---|
| `xbStringInput(label, name)` | `form-control`, `form-label fw-bold`, `mb-3` | Supports `setError(bool)` for validation feedback |
| `xbNumberInput(label, name)` | same as above | `type="number"` |
| `xbEmailInput(label, name)` | same as above | `type="email"` |
| `xbTelInput(label, name)` | same as above | `type="tel"` |
| `xbDateInput(label, name)` | same as above | `type="date"` |
| `xbRangeInput(label, name)` | same as above | `type="range"` |
| `xbPasswordInput(label, name)` | same as above | `type="password"` |
| `xbFileInput(label, name)` | same as above | `type="file"` |
| `xbMultiFileInput(label, name)` | same as above | Multiple file selection; shows an auto-updating file list |
| `xbTextAreaInput(label, name, rows)` | `form-control`, `form-label fw-bold` | Supports `setError(bool)` |
| `xbCheckBox(label, name)` | `form-check-input`, `form-check`, `form-check-label` | Supports `setError(bool)` |
| `xbRadioBtn(label, name)` | `form-check-input`, `form-check` | |
| `xbRadioBtnGrp(clss)` | — | Same as `xRadioBtnGrp` |
| `xbSelect(label, name)` | `form-select`, `form-label fw-bold`, `mb-3` | Supports `setError(bool)`, `getValues()` for multi-select |
| `xbMultiSelect(label, name)` | same as `xbSelect` | Multiple selection; use `getValues()` which returns an array |

### Date & Time Pickers

These use the **Tempus Dominus 6** library (must be included separately).

**`xbDateTimePicker(label, name, noManual, icon)`** — Full date and time picker. Call `.run()` after appending to the DOM to initialize the picker. Use `getValue()` / `setValue()` to read and write the value. Supports `setMinDate(date)` and `setMaxDate(date)`. Call `destroy()` to clean up.

**`xbDatePicker(label, name, noManual, icon)`** — Date-only picker (format `yyyy-MM-dd`). Extends `xbDateTimePicker`.

**`xbTimePicker(label, name, noManual, icon)`** — Time-only picker (format `HH:mm`). Extends `xbDateTimePicker`.

### Buttons

**`xbButton(label, clss)`** — A Bootstrap button (`btn btn-primary` by default).

**`xbButtonIcon(label, icon, right, clss)`** — A button with a Font Awesome icon. Pass `right=true` to place the icon after the label.

**`xbLink(label, href, clss)`** — A link styled as a Bootstrap button (`btn btn-outline-primary` by default).

**`xbButtonDropdown(label, icon, clss, wclss, dclss)`** — A split button with a dropdown menu. Add items with `addItem(xbButtonDropdownItem)`.

**`xbButtonDropdownItem(label, f, clss)`** — A single item in a button dropdown. Binds a click handler on construction.

**`xbButtonGroup(clss)`** — Horizontal button group (`btn-group`). Add buttons with `addButton(btn)`.

**`xbButtonGroupVertical(clss)`** — Vertical button group (`btn-group-vertical`).

### Forms

**`xbForm(clss)`** — A `<form>` container. Add any xScript input elements to it. Use `getFormData()` to retrieve all field values as a plain object keyed by field name. Use `validateForm()` to run all validators and visually mark any errors.

### Layout & Containers

**`xbPanel(clss)`** — Plain Bootstrap panel (unstyled wrapper).

**`xbTitlePanel(title, clss)`** — A Bootstrap card (`card`) with a `card-header` and `card-body`. Add elements to the body with `addElement()`. Update the title with `setTitle()`.

**`xbLayout(rows, cols, widths, clss)`** — Table-based grid, same API as `xLayout`.

**`xbJumbotron()`** — A Bootstrap 5 jumbotron-style hero section (`p-5 mb-4 bg-body-secondary rounded-lg`).

**`xbAlert(clss)`** — A Bootstrap alert box (`alert alert-info` by default). Add content with `addElement()`.

**`xbProgressBar(min, max, clss)`** — A Bootstrap progress bar. Update progress with `setProgress(value, optionalLabel)` where value is a percentage (0–100).

### Navigation

**`xbNavBar(title, clss, listClss)`** — A responsive Bootstrap navbar. Add navigation items using `addItem(xbNavBarItem)` or `addItem(xbNavBarDropdown)`. Update the brand title with `setTitle()`.

**`xbNavBarItem(label, clss)`** — A navbar link item. Bind click handlers with `bindFunction(f)`.

**`xbNavBarDropdown(title, clss)`** — A dropdown menu for the navbar. Add items with `addItem(xbNavBarItem)` and separators with `addDivider()`.

**`xbSideBar(clss)`** — A vertical sidebar navigation panel. Add items with `addItem(xbSideBarItem)` and collapsible groups with `addDropdown(label, xbSideBarDropdown)`.

**`xbSideBarItem(label, href, icon, clss)`** — A sidebar navigation link. Optionally pass a Font Awesome icon class. Toggle the active state with `setActive(bool)`.

**`xbSideBarDropdown(clss)`** — A collapsible group of items inside a sidebar. Add items with `addItem(xbSideBarItem)`. Set an ID with `setId(id)` to link it to a sidebar toggle.

### Tabs

**`xbTabs(clss)`** — A Bootstrap tabbed interface. Add tab panes with `addPane(title, xbTabPane)`. Programmatically switch tabs with `activatePane(index)` and rename a tab with `setPaneTitle(index, title)`.

**`xbTabPane(clss)`** — A content pane for use inside `xbTabs`. Add child elements with `addElement()`.

### Modals

**`xbModal(title, clss, dialogClss)`** — A Bootstrap modal dialog. Add content to the body with `addToBody(element)` and footer buttons with `addToFooter(element)`. Use `addCloseButton(label, clss)` as a shortcut to add a dismiss button. Show and hide with `show()` and `hide()`.

**`xbModalOpenButton(label, modal, clss)`** — A button that opens a specific `xbModal` when clicked.

### Lists

**`xbListGroup(clss)`** — A Bootstrap list group. Add items with `addItem(xbListGroupItem)`.

**`xbListGroupItem(label, clss)`** — An actionable list group item (styled as `list-group-item-action`). Bind click handlers with `bindFunction(f)`.

### Media

**`xbMediaList(clss)`** — A media list (`<ul>`).

**`xbMediaItem(heading, imgSrc, imgAlt, clss, imgClss)`** — A media object with an image on the left and a body on the right. Add content to the body with `addToBody(element)`. Bind a click handler on the image with `bindFunction(f)`.

### Miscellaneous

**`xbImage(src, alt, clss)`** — Bootstrap-flavored image (same API as `xImage`).

**`xbTextBox(text, clss)`** — Same as `xTextBox`.

**`xbPara(text, clss)`** — Same as `xPara`.

**`xbTheme(label, currentTheme)`** — A select dropdown for switching Bootstrap themes. Dynamically injects a `<link>` tag to load theme CSS from `/tools/css/bootstrap_themes/<theme>/bootstrap.min.css`. Use `setTheme(name)` to switch programmatically.

**`xbCarousel(clss)`** — A Bootstrap carousel. Add slides with `addSlide(xbSlide)`.

**`xbSlide(imgSrc, caption, imgClss)`** — A single carousel slide with an image and optional caption element.

---

## Example: Building a Form

```javascript
// Create a Bootstrap-styled form
var form = new xbForm();
var name  = new xbStringInput("Full Name", "fname");
var email = new xbEmailInput("Email", "email");
var msg   = new xbTextAreaInput("Message", "msg", 4);
var btn   = new xbButton("Send", "btn-success");

name.addValidator(new xNotEmptyValidator());
email.addValidator(new xNotEmptyValidator());

btn.bindFunction(function() {
   if (form.validateForm()) {
      var data = form.getFormData();
      console.log(data); // { fname: "...", email: "...", msg: "..." }
   }
});

form.addElement(name)
    .addElement(email)
    .addElement(msg)
    .addElement(btn);

form.append(document.body);
```

---

## License

Copyright © 2026 Xavier Llamas Rolland. Released under the [GPLv3 License](https://www.gnu.org/licenses/gpl-3.0.html).

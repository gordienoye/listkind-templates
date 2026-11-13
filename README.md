# ListKind templates

The templates the ListKind app offers when someone makes a new list: a recipe with its
tools, ingredients and steps, a routine, a checklist. Each build of the app bundles a pinned
release of this repository, and later versions of the app will browse it to show which
templates are in use, which can be upgraded and which are new.

Templates hold data only, never code, and are free for anyone to use in any way
([CC0 1.0](LICENSE)).

## Layout

```
catalogue.json                      every template, version and file, with its SHA-256
templates/
  <publisher>/<name>/
    <version>.<language>.md         one file per version and language, never changed once published
    CHANGES.md                      notes for each version, shown before an upgrade
tools/templatecheck.jar             the checks, built from the app's own template reader
```

## A template file

```markdown
---
listkind: 1
template: listkind/recipe
version: 1.0.0
language: en
type: sections
collection: recipes
summary: A recipe: tools, ingredients and steps.
author: ListKind
licence: CC0-1.0
---
# Recipe

## Tools <!-- key=tools role=tools type=checklist also="Equipment|Utensils|You will need" -->

## Ingredients <!-- key=ingredients role=ingredients type=checklist also="What you need" -->

## Steps <!-- key=steps role=steps type=steps also="Method|Directions|Instructions" -->
```

- **The header** (`key: value` lines between `---` lines):
  - `listkind` is the file format version;
  - `template` is the id, `publisher/name` in lowercase letters, digits and hyphens;
  - `version` is `major.minor.patch`;
  - `language` is the language code (`en`, `fr`, `pt-br`);
  - `type` is `checklist`, `steps` or `sections`;
  - `collection` (`routines`, `recipes`, `planning`, `shopping` or `other`), `summary`,
    `author` and `licence` are optional.
- **The title** is the `#` heading.
- **A `sections` template** has one `##` heading per list. Its comment gives:
  - `key`, fixed for the life of the template so lists made from it keep their link to
    each section;
  - `type`, `checklist` or `steps`;
  - `role` (`tools`, `ingredients`, `steps` or `other`), which the app uses, for example
    to find a recipe's ingredients for the pantry check;
  - `also`, other names people give the heading, separated by `|`, matched when a document
    is imported.
- **Starting items**, which every new list gets, are list lines with a key:
  `- Phone charger <!-- key=charger -->`. Use `1.` lines for steps and `-` lines for
  checklists.
- **Translations** are files with the same id and version, their own `language`, and the
  same sections, types, roles and keys.

## Versions

- **patch:** wording that does not reach lists (the summary, the other names);
- **minor:** additions (a section, a starting item);
- **major:** anything else (a section removed, a type or role changed, sections reordered).

A published file is never edited. A change is a new file with a new version, a `## <version>`
heading in the template's `CHANGES.md`, and the catalogue written again.

## Checking a change

```sh
java -jar tools/templatecheck.jar catalogue .   # writes catalogue.json from the files
java -jar tools/templatecheck.jar check .       # runs every check, as CI does
```

Pull requests are checked by CI and reviewed before merging. Releases are tagged
`release-<n>`; the app pins one and bundles its templates.

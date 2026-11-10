# Bristol Flight Lab docs

The source for <https://bristolflightlab.github.io>, the documentation site of
[Bristol Flight Lab](https://flightlab.bristol.ac.uk) at the University of
Bristol.

## What it's for

The site is mainly for internal use: lab admin, such as how to get to the
Field Robotics Facility, and training material for our team, postgraduates,
and undergraduate students.

We share it publicly in case it's useful to others. If you spot an error or
want to suggest a change, please open an
[issue](https://github.com/BristolFlightLab/bristolflightlab.github.io/issues)
or a pull request.

## Licence

Content is released under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/),
except where otherwise indicated. You're free to share and adapt it, as long as
you credit Bristol Flight Lab. The Flight Lab and University of Bristol logos
and artwork are not covered by this licence.

## How the site is built

The site is built with [Zensical](https://zensical.org) and styled with
[flightlab-zensical-theme](https://github.com/BristolFlightLab/flightlab-zensical-theme),
which is included as a Git submodule in `theme/`, pinned to a tag.

- Pages are Markdown files in `docs/`. The navigation is built from the folder
  structure, so a new page appears without any config changes.
- Site settings are in `zensical.toml`.
- Every push to `main` is built and deployed to GitHub Pages by
  `.github/workflows/ci.yml`. Pull requests are built but not deployed, so a
  broken build shows up before it is merged.

## Working on it locally

Clone with the theme submodule:

```bash
git clone --recurse-submodules https://github.com/BristolFlightLab/bristolflightlab.github.io
```

If you've already cloned it without the submodule:

```bash
git submodule update --init
```

Install Zensical (a virtual environment is a good idea):

```bash
pip install -r requirements.txt
```

Start a local preview, which reloads as you edit:

```bash
zensical serve
```

Then open the address it prints, usually <http://localhost:8000>.

To upgrade the theme, check out a newer tag in the submodule and commit it:

```bash
git -C theme fetch --tags && git -C theme checkout vX.Y.Z
git add theme && git commit -m "Update the site theme to vX.Y.Z"
```

## Writing pages

- Write in British English.
- Start each page with a single `#` heading, which becomes its title in the
  navigation.
- An `index.md` in a folder becomes that section's landing page.
- Maths goes between `$...$` (inline) or `$$...$$` (display), and is rendered
  with MathJax.
- Admonitions (`!!! note`, `!!! warning`), content tabs, and task lists are
  all available. See the
  [Zensical documentation](https://zensical.org/docs/) for the full set.
- Don't put real passwords or other credentials on a page: everything here is
  public.

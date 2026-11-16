# commit-art

Draw text art on your GitHub contribution calendar using dated Git commits.
Requires Python 3.8+ and Git 2.28+. Automatic ink shading also requires
Pillow 10.1+ (`python3 -m pip install "Pillow>=10.1"`).

## Preview and generate

Save this as `art.txt` to try a shaded diamond.

```text
...1...
..121..
.12321.
1234321
.12321.
..121..
...1...
```

```sh
./commit-art --file art.txt --year 2023 --preview
./commit-art --file art.txt --year 2023 --output ./my-art --email 'YOUR_GITHUB_EMAIL'
```

Pass the art file with `--file` (`-f`) or as the first positional argument.
Use `-` as the filename to read from stdin. Use `--range START END` for an
inclusive span of up to 366 days.

```sh
./commit-art art.txt --range 2024-01-07 2024-02-24 --preview
```

The first column is the week containing the start date. Rows run from Sunday at
the top to Saturday at the bottom. Each column is one week, so a full calendar
year usually spans 53 columns. A leap year starting on Saturday spans 54.
With `--year`, the CLI keeps the original placement when it fits. If a partial
week would cut off the drawing, it shifts the drawing by the fewest weeks that
make it fit. For full-width art that needs more room, it compresses the columns
and keeps the darkest shade when two painted cells overlap. Weekday rows stay
fixed. Year mode accepts drawings up to 54 columns wide.

With `--range`, placement is exact. Keep the days before the start and after
the end blank in partial weeks. The preview marks unavailable cells with `-`
and available blank cells with `.`.

| Input | Commits per day |
| --- | ---: |
| Space, `.`, `0` | 0 |
| `1`, `:` | 1 |
| `2`, `+` | 4 |
| `3`, `*` | 8 |
| `4`, `#` | 16 |

If a UTF-8 file includes characters outside this palette, the CLI estimates
how much ink each glyph uses in a font and ranks the distinct densities across
the drawing. Lighter glyphs get fewer commits, darker glyphs get more, and
equal densities share a shade. The Unicode fills `░▒▓█` have increasing
densities. Whitespace and the empty Braille cell are blank. In ink mode a dot
is a light glyph, so use spaces for the background.

Use `--shading ink` to measure glyphs even when the file uses only built-in
characters. Each character occupies one calendar cell. Ink estimates can vary
with the installed fonts, and glyphs that the available fonts can't render
are treated as filled cells.

Change the four counts with `--counts 1,5,12,30`. The preview shows your intended
shades and total commit count. GitHub assigns shades relative to other days in
the calendar, and existing contributions can change the appearance of any cell,
including blank ones.
[GitHub contribution calendar fields](https://docs.github.com/en/graphql/reference/users#contributioncalendarday)

## Publish to GitHub

The output directory must be new. It receives a standalone repository with one
commit per contribution on `main`. The CLI assigns both Git timestamps to noon
in your system's local time zone, using the UTC offset in effect on each selected
day. It reads the identity from Git configuration in the
directory you run it from. Override it with `--name` and `--email` as needed.

Create an empty public GitHub repository under your account and push the generated
`main` branch. Use an author email associated with your GitHub account, and keep
`main` as the default branch. To use a private repository, enable private
contributions on your profile.
[GitHub contribution criteria](https://docs.github.com/en/account-and-profile/reference/profile-contributions-reference)

```sh
git -C ./my-art remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO.git
git -C ./my-art push -u origin main
```

On your GitHub profile, select the year you drew in to see the result. A span
crossing December and January can appear across two year views.

## Undo the art

```sh
./commit-art --undo ./my-art
./commit-art --undo ./my-art --push  # Also update origin/main if you published the art
```

Undo replaces the generated commits on `main` with a single empty commit. The
repository stays in place and keeps a local reference to the generated history.
Undo requires a clean working tree on `main` and refuses to replace later local
commits. With `--push`, the remote update uses the original generated tip as a
lease, so it also refuses to replace later commits on `origin/main`. GitHub
branch protection can block this update.

Repositories created before undo support lack the saved references this command
uses.

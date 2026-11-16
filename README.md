# commit-art

Draw ASCII art on your GitHub contribution calendar using dated Git commits.
Requires Python 3.8+ and Git 2.28+.

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
./commit-art art.txt --year 2023 --preview
./commit-art art.txt --year 2023 --output ./my-art --email 'YOUR_GITHUB_EMAIL'
```

Use `--range START END` for an inclusive span of up to 366 days. Use `-` as
the art filename to read from stdin.

```sh
./commit-art art.txt --range 2024-01-07 2024-02-24 --preview
```

The first column is the week containing the start date. Rows run from Sunday at
the top to Saturday at the bottom. Each column is one week, so a full calendar
year usually spans 53 columns. A leap year starting on Saturday spans 54.
Keep the days before the start and after the end blank in partial weeks. The
preview marks those cells with `-` and available blank cells with `.`.

| Input | Commits per day |
| --- | ---: |
| Space, `.`, `0` | 0 |
| `1`, `:` | 1 |
| `2`, `+` | 4 |
| `3`, `*` | 8 |
| `4`, `#`, any other printable ASCII character | 16 |

Change the four counts with `--counts 1,5,12,30`. The preview shows your intended
shades and total commit count. GitHub assigns shades relative to other days in
the calendar, and existing contributions can change the appearance of any cell,
including blank ones.
[GitHub contribution calendar fields](https://docs.github.com/en/graphql/reference/users#contributioncalendarday)

## Publish to GitHub

The output directory must be new. It receives a standalone repository with one
commit per contribution on `main`. The CLI assigns both Git timestamps to noon
UTC on each selected day. It reads the identity from Git configuration in the
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

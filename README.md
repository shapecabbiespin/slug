# slug

**Rename messy files to URL-friendly names. Stdlib only, ~20 lines.**

```
My Vacation Photos (2).JPG  ->  my-vacation-photos-(2).jpg
```

## Why?

Files named `My Report FINAL v2 (1).docx` are a pain to share, upload or put in URLs.
`slug` cleans them: lowercase, spaces become dashes, junk characters removed.
Dry-run by default — it prints what it *would* do and touches nothing until you say so.

## Use

Save as `slug.py`, then:

```bash
python slug.py          # show what would be renamed (safe)
python slug.py --yes    # actually do it
```

## The whole script

```python
#!/usr/bin/env python3
"""slug — rename files in the current dir to URL-friendly names."""
import re
import sys
from pathlib import Path


def slug(name: str) -> str:
    name = name.strip().lower().replace(" ", "-")
    name = re.sub(r"[^a-z0-9._-]", "", name)
    return re.sub(r"-{2,}", "-", name).strip("-")


def main() -> None:
    do_it = "--yes" in sys.argv
    for p in sorted(Path(".").iterdir()):
        if not p.is_file():
            continue
        new = slug(p.name)
        if new == p.name:
            continue
        if not slug(p.stem):
            print(f"SKIP {p.name}  (nothing left after cleaning)")
            continue
        print(f"{p.name}  ->  {new}")
        if do_it:
            p.rename(new)


if __name__ == "__main__":
    main()
```

## Notes

- Works on the current directory only, files only, one level deep.
- Non-ASCII characters (Cyrillic, emoji, ...) are removed, not transliterated. Keep that in mind for names like `фото.jpg`.
- If two files would get the same new name, the second rename fails with an error. Rename the first one and run again.

## License

MIT. Shorter than this sentence: just use it.

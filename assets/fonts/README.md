# Bundled fonts

The map and interface use self-hosted WOFF2 subsets of **Noto Serif SC**, **Noto
Sans SC**, and **Ma Shan Zheng**. No Google Fonts stylesheet or font request is
needed at runtime. Each family has one consolidated font face; the Noto files
retain their variable weights. The regular generic CSS fallbacks are only for
loading failures or text outside the bundled coverage.

Source: [google/fonts](https://github.com/google/fonts/tree/e7db6ca603fd4862010383ff17ffd6cc4ffac1c0/ofl),
pinned to revision `e7db6ca603fd4862010383ff17ffd6cc4ffac1c0`. The three included
`OFL-*.txt` files retain the respective copyright notices and SIL Open Font License
1.1. `manifest.json` records source URLs, source/output SHA-256 hashes and the
actual output character coverage.

The serif and sans subsets include characters from the application's HTML, CSS
and JavaScript, including historical data, the English dictionary and the
simplified/traditional conversion table, plus Latin text. All current CJK
characters are covered in both fonts. Calligraphy is limited to the masthead and
dynasty titles; unsupported calligraphy characters use the bundled serif font.
The three files total 5,298,656 bytes (about 5.05 MiB): approximately 2.91 MiB serif,
2.11 MiB sans and 38 KiB calligraphy. These are first-download costs; content-hashed
filenames allow ordinary browser/hosting HTTP caching without stale font reuse
after a rebuild. No service worker is required.

## Rebuild after changing text

The application has no added runtime dependencies. Font generation is an optional
development step using Python, FontTools and Brotli:

```sh
python3 -m venv /tmp/jiangshan-font-tools
/tmp/jiangshan-font-tools/bin/pip install fonttools==4.67.0 brotli==1.2.1
/tmp/jiangshan-font-tools/bin/python scripts/build_fonts.py \
  --source-dir /tmp/jiangshan-font-source
node scripts/validate_atlas.js
```

The source directory caches the three upstream TTF files; an empty directory
downloads them from the pinned revision. Commit the generated WOFF2 files,
`fonts.css` and `manifest.json` together. The builder replaces only its own old
content-hashed font files. Advance the uniform asset version in `index.html` and
`js/app.js` when changing the font stylesheet. The validator verifies file hashes,
WOFF2 signatures, current CJK coverage and required calligraphy title characters.
The browser regression also blocks all external hosts and checks the actual fonts
used to render map labels and the masthead, preventing silent system-font fallback.

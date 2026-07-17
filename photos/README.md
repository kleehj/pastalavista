# photos/

Card images for the app, referenced locally (not hotlinked) so they work on rural 4G and don't depend on any external host.

The app lazy-loads each and **falls back to a colored gradient if the file is missing**, so a missing photo never shows a broken image.

## Expected files (drop the real photos in with these exact names)

| Filename | Card it fills |
|---|---|
| `poderi-pool.jpg` | Farm Day (the infinity-pool-over-vineyard shot) |
| `monteriggioni.jpg` | Day shape 1 — Monteriggioni + Colle + River (aerial walled town) |
| `volterra.jpg` | Day shape 2 — Volterra (hilltop town) |
| `sentierelsa.jpg` | Afternoon water — SentierElsa (turquoise river + rope) |
| `san-gimignano.jpg` | Dinner · San Gimignano (towers skyline) |
| `certaldo.jpg` | Dinner · Certaldo Alto (sunset hill town) |

Landscape orientation, ~800–1600px wide, JPG. They're displayed cropped to a wide banner (`object-fit: cover`), so center the subject.

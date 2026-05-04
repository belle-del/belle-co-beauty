# Photo workflow

## Where photos go

| File path | Slot | Aspect ratio |
|---|---|---|
| `/photos/00-hero.jpg` | Hero image slot, top right | 4:5 |
| `/photos/01.jpg` | Plate 01, Wefts feature | 5:6 |
| `/photos/02.jpg` | Plate 02, K-tips | 4:3 |
| `/photos/03.jpg` | Plate 03, Tape-ins | 4:5 |
| `/photos/04.jpg` | Plate 04, Vivid color | 3:4 |
| `/photos/05.jpg` | Plate 05, Color correction wide | 16:9 |
| `/photos/06.jpg` | Plate 06, Bleach retouch | 1:1 |
| `/photos/07.jpg` | Plate 07, Maintenance | 1:1 |
| `/photos/08.jpg` | Plate 08, Custom nail art | 1:1 |
| `/photos/09.jpg` | Plate 09, Detail work | 1:1 |

## Specs

- Format: JPG (universal, good compression for photos)
- Max width: 1600px on the long edge
- Quality: 80 to 85, compress to roughly 200 to 400KB per file
- Color space: sRGB

## Two ways to compress

1. Squoosh.app, drag and drop, pick MozJPEG, quality 82
2. ImageMagick, run `magick input.jpg -resize 1600x -quality 85 output.jpg`

## When you don't have a photo yet

Don't put the file. The placeholder gradient stays in place. The page never reads as broken.

## When you replace a photo

Just overwrite the file. Browser cache will show the old version until hard refresh. For a deployed Vercel build, you may need to trigger a redeploy if the photo filename did not change.

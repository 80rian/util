# Reels Cover Generator

Instagram Reels cover image generator. Upload an image, add a title, pick a style preset, and download a 1080x1920 PNG.

## Features

- **7 style presets** - Clean White, Hard Shadow, Dark Modern, Solid Label, Frosted Glass, Highlight Marker, Gradient Scrim
- **Auto color recommendation** - Analyzes image brightness and suggests the best preset
- **20+ fonts** - Bundled (NanumSquare, S-CoreDream), Google Fonts, Naver Fonts, macOS system fonts, and local font upload
- **Dual preview** - Reels (9:16) and Feed (1:1 center crop) side by side
- **Adjustable font size** - 40px to 140px slider

## Usage

Open `index.html` in a browser. No server required.

1. Upload an image (drag & drop or click)
2. Enter title text
3. Select a style preset
4. Choose a font and size
5. Download PNG (1080x1920)

## Font Sources

| Source | Fonts |
|--------|-------|
| Bundled | NanumSquare, S-CoreDream |
| Google Fonts | Noto Sans/Serif KR, Black Han Sans, Jua, Do Hyeon, Gothic A1, Gowun Dodum/Batang |
| Naver | NanumGothic, NanumMyeongjo, NanumBarunGothic |
| macOS | Apple SD Gothic Neo, SF Pro Display, Helvetica Neue, Avenir Next, Futura, Georgia |
| Upload | Any .ttf / .otf / .woff / .woff2 |

## Tech

Single HTML file. Vanilla JS + Canvas API. No build step, no dependencies.

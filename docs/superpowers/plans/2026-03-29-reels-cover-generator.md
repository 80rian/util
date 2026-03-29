# Reels Cover Generator Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 인스타그램 릴스 커버 이미지를 생성하는 Single HTML 웹 도구를 만든다.

**Architecture:** Single HTML file with inline CSS/JS. Canvas API로 이미지 합성. Google Fonts CDN으로 웹폰트 로드. 서버 불필요.

**Tech Stack:** HTML, CSS, JavaScript (Vanilla), Canvas API, Google Fonts, FontFace API

---

## File Structure

- Create: `index.html` — 전체 앱 (HTML + CSS + JS 인라인)

단일 파일이므로 파일 구조가 간단하다. HTML 내부의 JS는 논리적 섹션으로 구분한다:
1. **PRESETS** — 프리셋 정의 객체
2. **STATE** — 앱 상태 관리
3. **RENDER** — Canvas 렌더링 로직
4. **UI** — DOM 이벤트 핸들링
5. **UTILS** — 밝기 분석, 폰트 로딩 등

---

### Task 1: HTML 구조 + CSS 스타일링

**Files:**
- Create: `index.html`

- [ ] **Step 1: HTML 기본 구조 작성**

```html
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Reels Cover Generator</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@400;700;800;900&display=swap" rel="stylesheet">
  <style>
    /* Step 2에서 작성 */
  </style>
</head>
<body>
  <div class="app">
    <header class="header">
      <h1>Reels Cover Generator</h1>
    </header>

    <main class="main">
      <!-- 이미지 업로드 -->
      <section class="upload-section">
        <div class="dropzone" id="dropzone">
          <p class="dropzone-text">이미지를 드래그하거나 클릭하여 업로드</p>
          <input type="file" id="fileInput" accept="image/*" hidden>
        </div>
      </section>

      <!-- 타이틀 입력 -->
      <section class="input-section">
        <label for="titleInput">타이틀</label>
        <textarea id="titleInput" rows="2" placeholder="커버에 표시할 텍스트를 입력하세요"></textarea>
      </section>

      <!-- 프리셋 선택 -->
      <section class="preset-section">
        <label>프리셋</label>
        <div class="preset-recommendation" id="presetRecommendation" hidden>
          추천: <span id="recommendedPreset"></span>
        </div>
        <div class="preset-grid" id="presetGrid">
          <!-- JS에서 생성 -->
        </div>
      </section>

      <!-- 폰트 선택 -->
      <section class="font-section">
        <label for="fontSelect">폰트</label>
        <div class="font-controls">
          <select id="fontSelect">
            <option value="Noto Sans KR">Noto Sans KR</option>
          </select>
          <button id="fontUploadBtn" type="button">로컬 폰트 업로드</button>
          <input type="file" id="fontFileInput" accept=".ttf,.otf,.woff,.woff2" hidden>
        </div>
      </section>

      <!-- 미리보기 -->
      <section class="preview-section">
        <div class="preview-container">
          <div class="preview-item">
            <label>릴스 (9:16)</label>
            <canvas id="reelsCanvas" width="1080" height="1920"></canvas>
          </div>
          <div class="preview-item">
            <label>피드 (1:1)</label>
            <canvas id="feedCanvas" width="1080" height="1080"></canvas>
          </div>
        </div>
      </section>

      <!-- 다운로드 -->
      <section class="download-section">
        <button id="downloadBtn" type="button" disabled>PNG 다운로드</button>
      </section>
    </main>
  </div>

  <script>
    // Task 2부터 작성
  </script>
</body>
</html>
```

- [ ] **Step 2: CSS 작성**

`<style>` 태그 안에 다음 CSS를 작성한다:

```css
* { margin: 0; padding: 0; box-sizing: border-box; }

body {
  font-family: 'Noto Sans KR', -apple-system, sans-serif;
  background: #0f0f0f;
  color: #e0e0e0;
  min-height: 100vh;
}

.app {
  max-width: 900px;
  margin: 0 auto;
  padding: 1.5rem;
}

.header h1 {
  font-size: 1.4rem;
  font-weight: 700;
  margin-bottom: 1.5rem;
  color: #fff;
}

.main > section {
  margin-bottom: 1.5rem;
}

label {
  display: block;
  font-size: 0.85rem;
  font-weight: 700;
  color: #aaa;
  margin-bottom: 0.5rem;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

/* 드롭존 */
.dropzone {
  border: 2px dashed #444;
  border-radius: 12px;
  padding: 3rem 2rem;
  text-align: center;
  cursor: pointer;
  transition: border-color 0.2s, background 0.2s;
}
.dropzone:hover, .dropzone.dragover {
  border-color: #7c6aef;
  background: rgba(124, 106, 239, 0.05);
}
.dropzone-text { color: #888; }
.dropzone.has-image { padding: 0.5rem; border-style: solid; border-color: #333; }
.dropzone.has-image .dropzone-text { display: none; }
.dropzone .thumb { max-width: 200px; max-height: 120px; border-radius: 6px; }

/* 타이틀 입력 */
textarea {
  width: 100%;
  padding: 0.75rem 1rem;
  font-size: 1rem;
  font-family: inherit;
  background: #1a1a1a;
  border: 1px solid #333;
  border-radius: 8px;
  color: #fff;
  resize: vertical;
}
textarea:focus { outline: none; border-color: #7c6aef; }

/* 프리셋 */
.preset-recommendation {
  font-size: 0.85rem;
  color: #7c6aef;
  margin-bottom: 0.5rem;
}
.preset-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(110px, 1fr));
  gap: 0.5rem;
}
.preset-btn {
  background: #1a1a1a;
  border: 2px solid #333;
  border-radius: 8px;
  padding: 0.6rem;
  cursor: pointer;
  text-align: center;
  transition: border-color 0.2s;
  color: #ccc;
  font-size: 0.8rem;
  font-family: inherit;
}
.preset-btn:hover { border-color: #555; }
.preset-btn.active { border-color: #7c6aef; background: rgba(124, 106, 239, 0.1); }
.preset-btn .preset-preview {
  height: 48px;
  border-radius: 4px;
  margin-bottom: 0.4rem;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 800;
  font-size: 0.9rem;
  overflow: hidden;
}
.preset-btn .preset-name { font-size: 0.75rem; }

/* 폰트 */
.font-controls {
  display: flex;
  gap: 0.5rem;
  align-items: center;
}
select {
  flex: 1;
  padding: 0.6rem 0.75rem;
  font-size: 0.9rem;
  font-family: inherit;
  background: #1a1a1a;
  border: 1px solid #333;
  border-radius: 8px;
  color: #fff;
}
select:focus { outline: none; border-color: #7c6aef; }

#fontUploadBtn {
  padding: 0.6rem 1rem;
  font-size: 0.8rem;
  font-family: inherit;
  background: #2a2a2a;
  border: 1px solid #444;
  border-radius: 8px;
  color: #ccc;
  cursor: pointer;
  white-space: nowrap;
}
#fontUploadBtn:hover { background: #333; }

/* 미리보기 */
.preview-container {
  display: flex;
  gap: 1.5rem;
  align-items: flex-start;
}
.preview-item { flex-shrink: 0; }
.preview-item label { text-align: center; }
#reelsCanvas {
  width: 240px;
  height: 427px;
  background: #1a1a1a;
  border-radius: 8px;
  border: 1px solid #333;
}
#feedCanvas {
  width: 240px;
  height: 240px;
  background: #1a1a1a;
  border-radius: 8px;
  border: 1px solid #333;
}

/* 다운로드 */
#downloadBtn {
  width: 100%;
  padding: 0.9rem;
  font-size: 1rem;
  font-weight: 700;
  font-family: inherit;
  background: #7c6aef;
  color: #fff;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  transition: opacity 0.2s;
}
#downloadBtn:hover { opacity: 0.9; }
#downloadBtn:disabled { opacity: 0.4; cursor: not-allowed; }
```

- [ ] **Step 3: 브라우저에서 열어 레이아웃 확인**

Run: 파일을 브라우저에서 열어 모든 섹션이 올바르게 표시되는지 확인한다.
Expected: 다크 테마 UI. 드롭존, 텍스트 입력, 프리셋 그리드(빈 상태), 폰트 선택, 빈 캔버스 2개, 비활성 다운로드 버튼이 보인다.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: add HTML structure and CSS styling for reels cover generator"
```

---

### Task 2: 프리셋 정의 + 프리셋 UI 렌더링

**Files:**
- Modify: `index.html` (`<script>` 태그 내부)

- [ ] **Step 1: 프리셋 데이터 정의**

`<script>` 태그 안에 다음 코드를 작성한다:

```js
// ===== PRESETS =====
const PRESETS = [
  {
    id: 'clean-white',
    name: '클린 화이트',
    textColor: '#ffffff',
    fontWeight: 800,
    shadow: { offsetX: 0, offsetY: 2, blur: 12, color: 'rgba(0,0,0,0.6)' },
    background: null,
    overlay: null,
    previewBg: '#3a5a40',
    lightBackground: false,
  },
  {
    id: 'hard-shadow',
    name: '하드 섀도우',
    textColor: '#ffffff',
    fontWeight: 800,
    shadow: { offsetX: 4, offsetY: 4, blur: 0, color: 'rgba(0,0,0,0.8)' },
    background: null,
    overlay: null,
    previewBg: '#5c4033',
    lightBackground: false,
  },
  {
    id: 'dark-modern',
    name: '다크 모던',
    textColor: '#1a1a1a',
    fontWeight: 800,
    shadow: null,
    background: null,
    overlay: null,
    previewBg: '#e8dcc8',
    lightBackground: true,
  },
  {
    id: 'solid-label',
    name: '솔리드 라벨',
    textColor: '#ffffff',
    fontWeight: 700,
    shadow: null,
    background: { color: 'rgba(0,0,0,0.75)', paddingX: 24, paddingY: 12, radius: 8 },
    overlay: null,
    previewBg: '#764ba2',
    lightBackground: false,
  },
  {
    id: 'frosted-glass',
    name: '프로스트 글래스',
    textColor: '#ffffff',
    fontWeight: 700,
    shadow: null,
    background: { color: 'rgba(255,255,255,0.18)', paddingX: 30, paddingY: 16, radius: 16, blur: 20, border: 'rgba(255,255,255,0.25)' },
    overlay: null,
    previewBg: '#2193b0',
    lightBackground: false,
  },
  {
    id: 'highlight',
    name: '하이라이트 마커',
    textColor: '#1a1a1a',
    fontWeight: 800,
    shadow: null,
    background: { color: 'rgba(255,220,50,0.75)', paddingX: 14, paddingY: 4, radius: 4, perLine: true },
    overlay: null,
    previewBg: '#2a2a2a',
    lightBackground: false,
  },
  {
    id: 'gradient-scrim',
    name: '그라디언트 스크림',
    textColor: '#ffffff',
    fontWeight: 800,
    shadow: { offsetX: 0, offsetY: 1, blur: 8, color: 'rgba(0,0,0,0.4)' },
    background: null,
    overlay: { type: 'gradient', from: 'rgba(0,0,0,0)', to: 'rgba(0,0,0,0.65)', position: 0.45 },
    previewBg: '#8B7355',
    lightBackground: false,
  },
];
```

- [ ] **Step 2: 앱 상태 + 프리셋 UI 렌더링**

프리셋 정의 아래에 다음 코드를 추가한다:

```js
// ===== STATE =====
const state = {
  image: null,        // HTMLImageElement
  title: '',
  preset: PRESETS[0],
  fontFamily: 'Noto Sans KR',
};

// ===== UI =====
const $ = (sel) => document.querySelector(sel);

function renderPresetGrid() {
  const grid = $('#presetGrid');
  grid.innerHTML = PRESETS.map(p => `
    <button class="preset-btn ${p.id === state.preset.id ? 'active' : ''}"
            data-preset-id="${p.id}" type="button">
      <div class="preset-preview" style="background:${p.previewBg};">
        <span style="
          color:${p.textColor};
          font-weight:${p.fontWeight};
          ${p.shadow ? `text-shadow:${p.shadow.offsetX}px ${p.shadow.offsetY}px ${p.shadow.blur}px ${p.shadow.color}` : ''}
          ${p.background && !p.background.perLine ? `;background:${p.background.color};padding:2px 6px;border-radius:${p.background.radius}px` : ''}
          ${p.background && p.background.perLine ? `;background:${p.background.color};padding:1px 5px;border-radius:${p.background.radius}px` : ''}
        ">가나다</span>
      </div>
      <div class="preset-name">${p.name}</div>
    </button>
  `).join('');

  grid.addEventListener('click', (e) => {
    const btn = e.target.closest('.preset-btn');
    if (!btn) return;
    const preset = PRESETS.find(p => p.id === btn.dataset.presetId);
    if (preset) {
      state.preset = preset;
      renderPresetGrid();
      render();
    }
  });
}

renderPresetGrid();
```

- [ ] **Step 3: 브라우저에서 확인**

Run: 브라우저 새로고침
Expected: 프리셋 7개가 그리드에 표시. 각각 미니 미리보기와 이름 포함. 첫 번째(클린 화이트)가 활성 상태. 클릭하면 활성 상태가 변경된다 (render 함수 미구현이므로 에러 발생은 정상).

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: add preset definitions and preset grid UI"
```

---

### Task 3: 이미지 업로드 (드래그&드롭 + 파일 선택)

**Files:**
- Modify: `index.html` (`<script>` 태그 내부)

- [ ] **Step 1: 이미지 업로드 핸들러 작성**

`renderPresetGrid()` 호출 아래에 다음 코드를 추가한다:

```js
// ===== IMAGE UPLOAD =====
function loadImage(file) {
  if (!file || !file.type.startsWith('image/')) return;
  const reader = new FileReader();
  reader.onload = (e) => {
    const img = new Image();
    img.onload = () => {
      state.image = img;
      showThumbnail(img);
      analyzeAndRecommend(img);
      render();
      $('#downloadBtn').disabled = false;
    };
    img.src = e.target.result;
  };
  reader.readAsDataURL(file);
}

function showThumbnail(img) {
  const dropzone = $('#dropzone');
  dropzone.classList.add('has-image');
  const existing = dropzone.querySelector('.thumb');
  if (existing) existing.remove();
  const thumb = document.createElement('img');
  thumb.src = img.src;
  thumb.className = 'thumb';
  dropzone.appendChild(thumb);
}

// 드롭존 이벤트
const dropzone = $('#dropzone');
const fileInput = $('#fileInput');

dropzone.addEventListener('click', () => fileInput.click());
dropzone.addEventListener('dragover', (e) => {
  e.preventDefault();
  dropzone.classList.add('dragover');
});
dropzone.addEventListener('dragleave', () => dropzone.classList.remove('dragover'));
dropzone.addEventListener('drop', (e) => {
  e.preventDefault();
  dropzone.classList.remove('dragover');
  loadImage(e.dataTransfer.files[0]);
});
fileInput.addEventListener('change', () => {
  loadImage(fileInput.files[0]);
});
```

- [ ] **Step 2: 브라우저에서 확인**

Run: 이미지를 드래그&드롭하거나 클릭하여 업로드
Expected: 드롭존에 썸네일 표시, 다운로드 버튼 활성화 (render/analyzeAndRecommend 미구현 에러는 정상)

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add image upload with drag-and-drop"
```

---

### Task 4: Canvas 렌더링 엔진

**Files:**
- Modify: `index.html` (`<script>` 태그 내부)

- [ ] **Step 1: 핵심 render 함수 작성**

이미지 업로드 코드 아래에 다음을 추가한다:

```js
// ===== RENDER =====
function render() {
  if (!state.image) return;
  renderReelsCanvas();
  renderFeedCanvas();
}

function renderReelsCanvas() {
  const canvas = $('#reelsCanvas');
  const ctx = canvas.getContext('2d');
  const W = 1080, H = 1920;
  canvas.width = W;
  canvas.height = H;

  // 1. 배경 이미지 (cover fit)
  drawImageCover(ctx, state.image, W, H);

  // 2. 오버레이 (그라디언트 스크림 등)
  if (state.preset.overlay) {
    drawOverlay(ctx, state.preset.overlay, W, H);
  }

  // 3. 텍스트
  drawTitle(ctx, state.title, state.preset, W, H);
}

function renderFeedCanvas() {
  const reelsCanvas = $('#reelsCanvas');
  const feedCanvas = $('#feedCanvas');
  const ctx = feedCanvas.getContext('2d');
  const size = 1080;
  feedCanvas.width = size;
  feedCanvas.height = size;

  // 9:16 캔버스의 중앙을 1:1로 크롭
  const cropY = (1920 - size) / 2; // 420
  ctx.drawImage(reelsCanvas, 0, cropY, size, size, 0, 0, size, size);
}

function drawImageCover(ctx, img, W, H) {
  const imgRatio = img.width / img.height;
  const canvasRatio = W / H;
  let sx, sy, sw, sh;

  if (imgRatio > canvasRatio) {
    // 이미지가 더 넓음 → 좌우 크롭
    sh = img.height;
    sw = sh * canvasRatio;
    sx = (img.width - sw) / 2;
    sy = 0;
  } else {
    // 이미지가 더 좁음 → 상하 크롭
    sw = img.width;
    sh = sw / canvasRatio;
    sx = 0;
    sy = (img.height - sh) / 2;
  }

  ctx.drawImage(img, sx, sy, sw, sh, 0, 0, W, H);
}

function drawOverlay(ctx, overlay, W, H) {
  if (overlay.type === 'gradient') {
    const startY = H * overlay.position;
    const grad = ctx.createLinearGradient(0, startY, 0, H);
    grad.addColorStop(0, overlay.from);
    grad.addColorStop(1, overlay.to);
    ctx.fillStyle = grad;
    ctx.fillRect(0, 0, W, H);
  }
}
```

- [ ] **Step 2: drawTitle 함수 작성**

```js
function drawTitle(ctx, text, preset, W, H) {
  if (!text.trim()) return;

  const fontSize = Math.round(W * 0.07); // 약 76px at 1080w
  ctx.font = `${preset.fontWeight} ${fontSize}px '${state.fontFamily}', sans-serif`;
  ctx.textAlign = 'center';
  ctx.textBaseline = 'middle';

  const lines = text.split('\n').filter(l => l.trim());
  const lineHeight = fontSize * 1.4;
  const totalHeight = lines.length * lineHeight;
  const startY = (H - totalHeight) / 2 + lineHeight / 2;

  // 배경 (솔리드 라벨, 프로스트 글래스, 하이라이트 마커)
  if (preset.background) {
    drawTextBackground(ctx, lines, preset, W, startY, fontSize, lineHeight);
  }

  // 그림자
  if (preset.shadow) {
    ctx.shadowOffsetX = preset.shadow.offsetX;
    ctx.shadowOffsetY = preset.shadow.offsetY;
    ctx.shadowBlur = preset.shadow.blur;
    ctx.shadowColor = preset.shadow.color;
  }

  // 텍스트
  ctx.fillStyle = preset.textColor;
  lines.forEach((line, i) => {
    ctx.fillText(line.trim(), W / 2, startY + i * lineHeight);
  });

  // 그림자 리셋
  ctx.shadowOffsetX = 0;
  ctx.shadowOffsetY = 0;
  ctx.shadowBlur = 0;
  ctx.shadowColor = 'transparent';
}

function drawTextBackground(ctx, lines, preset, W, startY, fontSize, lineHeight) {
  const bg = preset.background;

  if (bg.perLine) {
    // 하이라이트 마커: 줄별 배경
    lines.forEach((line, i) => {
      const metrics = ctx.measureText(line.trim());
      const tw = metrics.width;
      const x = W / 2 - tw / 2 - bg.paddingX;
      const y = startY + i * lineHeight - fontSize / 2 - bg.paddingY;
      const w = tw + bg.paddingX * 2;
      const h = fontSize + bg.paddingY * 2;
      roundRect(ctx, x, y, w, h, bg.radius, bg.color);
    });
  } else if (bg.blur) {
    // 프로스트 글래스: 블러 영역
    const maxWidth = Math.max(...lines.map(l => ctx.measureText(l.trim()).width));
    const x = W / 2 - maxWidth / 2 - bg.paddingX;
    const y = startY - fontSize / 2 - bg.paddingY;
    const w = maxWidth + bg.paddingX * 2;
    const h = lines.length * lineHeight + bg.paddingY * 2 - (lineHeight - fontSize);
    drawFrostedGlass(ctx, x, y, w, h, bg);
  } else {
    // 솔리드 라벨: 단일 박스
    const maxWidth = Math.max(...lines.map(l => ctx.measureText(l.trim()).width));
    const x = W / 2 - maxWidth / 2 - bg.paddingX;
    const y = startY - fontSize / 2 - bg.paddingY;
    const w = maxWidth + bg.paddingX * 2;
    const h = lines.length * lineHeight + bg.paddingY * 2 - (lineHeight - fontSize);
    roundRect(ctx, x, y, w, h, bg.radius, bg.color);
  }
}

function drawFrostedGlass(ctx, x, y, w, h, bg) {
  // 영역 픽셀을 추출 → 블러 → 반투명 오버레이
  const tempCanvas = document.createElement('canvas');
  tempCanvas.width = w;
  tempCanvas.height = h;
  const tempCtx = tempCanvas.getContext('2d');

  // 해당 영역 복사
  const sourceCanvas = $('#reelsCanvas');
  tempCtx.drawImage(sourceCanvas, x, y, w, h, 0, 0, w, h);

  // 다운스케일 → 업스케일로 블러 근사
  const scale = 0.1;
  const smallW = Math.max(1, Math.round(w * scale));
  const smallH = Math.max(1, Math.round(h * scale));
  const smallCanvas = document.createElement('canvas');
  smallCanvas.width = smallW;
  smallCanvas.height = smallH;
  const smallCtx = smallCanvas.getContext('2d');
  smallCtx.drawImage(tempCanvas, 0, 0, smallW, smallH);

  // 블러된 이미지를 원래 크기로 그리기
  ctx.save();
  ctx.beginPath();
  ctx.roundRect(x, y, w, h, bg.radius);
  ctx.clip();
  ctx.drawImage(smallCanvas, 0, 0, smallW, smallH, x, y, w, h);

  // 반투명 오버레이
  ctx.fillStyle = bg.color;
  ctx.fill();

  // 테두리
  if (bg.border) {
    ctx.strokeStyle = bg.border;
    ctx.lineWidth = 2;
    ctx.stroke();
  }
  ctx.restore();
}

function roundRect(ctx, x, y, w, h, r, fillColor) {
  ctx.beginPath();
  ctx.roundRect(x, y, w, h, r);
  ctx.fillStyle = fillColor;
  ctx.fill();
}
```

- [ ] **Step 3: 타이틀 입력 이벤트 연결**

```js
// 타이틀 입력 실시간 반영
$('#titleInput').addEventListener('input', (e) => {
  state.title = e.target.value;
  render();
});
```

- [ ] **Step 4: 브라우저에서 확인**

Run: 이미지를 업로드하고 타이틀을 입력한다.
Expected: 릴스 캔버스에 이미지 + 중앙 텍스트가 표시. 피드 캔버스에 중앙 크롭 결과 표시. 프리셋을 바꾸면 텍스트 스타일이 변경된다.

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: add canvas rendering engine with all preset styles"
```

---

### Task 5: 자동 색상 추천

**Files:**
- Modify: `index.html` (`<script>` 태그 내부)

- [ ] **Step 1: 밝기 분석 + 추천 함수 작성**

render 함수 아래에 다음을 추가한다:

```js
// ===== AUTO RECOMMEND =====
function analyzeAndRecommend(img) {
  const canvas = document.createElement('canvas');
  const size = 100; // 분석용 소형 캔버스
  canvas.width = size;
  canvas.height = size;
  const ctx = canvas.getContext('2d');

  // 이미지 중앙부를 추출 (1:1 크롭 영역과 동일)
  const imgRatio = img.width / img.height;
  const targetRatio = 9 / 16;
  let sx, sy, sw, sh;
  if (imgRatio > targetRatio) {
    sh = img.height;
    sw = sh * targetRatio;
    sx = (img.width - sw) / 2;
    sy = 0;
  } else {
    sw = img.width;
    sh = sw / targetRatio;
    sx = 0;
    sy = (img.height - sh) / 2;
  }

  // 중앙 1/3 영역만 분석 (텍스트가 위치할 곳)
  const centerSx = sx + sw * 0.2;
  const centerSy = sy + sh * 0.33;
  const centerSw = sw * 0.6;
  const centerSh = sh * 0.34;

  ctx.drawImage(img, centerSx, centerSy, centerSw, centerSh, 0, 0, size, size);
  const data = ctx.getImageData(0, 0, size, size).data;

  let totalLuminance = 0;
  const pixelCount = size * size;
  for (let i = 0; i < data.length; i += 4) {
    totalLuminance += 0.299 * data[i] + 0.587 * data[i + 1] + 0.114 * data[i + 2];
  }
  const avgLuminance = totalLuminance / pixelCount; // 0-255

  // 밝으면 어두운 텍스트 프리셋, 어두우면 밝은 텍스트 프리셋 추천
  const recommended = avgLuminance > 140
    ? PRESETS.find(p => p.lightBackground)  // 다크 모던
    : PRESETS[0];                            // 클린 화이트

  state.preset = recommended;
  renderPresetGrid();

  const recEl = $('#presetRecommendation');
  recEl.hidden = false;
  $('#recommendedPreset').textContent = recommended.name;
}
```

- [ ] **Step 2: 브라우저에서 확인**

Run: 밝은 이미지와 어두운 이미지를 각각 업로드
Expected: 밝은 이미지 → "다크 모던" 추천 + 자동 선택. 어두운 이미지 → "클린 화이트" 추천 + 자동 선택.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add auto color recommendation based on image brightness"
```

---

### Task 6: 로컬 폰트 업로드

**Files:**
- Modify: `index.html` (`<script>` 태그 내부)

- [ ] **Step 1: 폰트 업로드 핸들러 작성**

자동 색상 추천 코드 아래에 다음을 추가한다:

```js
// ===== FONT UPLOAD =====
$('#fontUploadBtn').addEventListener('click', () => $('#fontFileInput').click());

$('#fontFileInput').addEventListener('change', async (e) => {
  const file = e.target.files[0];
  if (!file) return;

  const fontName = file.name.replace(/\.(ttf|otf|woff2?)$/i, '');
  const arrayBuffer = await file.arrayBuffer();
  const fontFace = new FontFace(fontName, arrayBuffer);

  try {
    const loaded = await fontFace.load();
    document.fonts.add(loaded);

    // 드롭다운에 추가
    const select = $('#fontSelect');
    const option = document.createElement('option');
    option.value = fontName;
    option.textContent = `${fontName} (로컬)`;
    select.appendChild(option);
    select.value = fontName;

    state.fontFamily = fontName;
    render();
  } catch (err) {
    alert('폰트를 로드할 수 없습니다: ' + err.message);
  }
});

$('#fontSelect').addEventListener('change', (e) => {
  state.fontFamily = e.target.value;
  render();
});
```

- [ ] **Step 2: 브라우저에서 확인**

Run: 로컬 .ttf 파일을 업로드
Expected: 드롭다운에 폰트 이름 추가, 캔버스 텍스트가 해당 폰트로 변경

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add local font upload via FontFace API"
```

---

### Task 7: PNG 다운로드

**Files:**
- Modify: `index.html` (`<script>` 태그 내부)

- [ ] **Step 1: 다운로드 핸들러 작성**

폰트 코드 아래에 다음을 추가한다:

```js
// ===== DOWNLOAD =====
$('#downloadBtn').addEventListener('click', () => {
  const canvas = $('#reelsCanvas');
  const link = document.createElement('a');
  const timestamp = new Date().toISOString().slice(0, 10);
  const safeTitle = state.title.split('\n')[0].trim().slice(0, 20).replace(/[^가-힣a-zA-Z0-9]/g, '_') || 'cover';
  link.download = `reels-cover-${safeTitle}-${timestamp}.png`;
  link.href = canvas.toDataURL('image/png');
  link.click();
});
```

- [ ] **Step 2: 브라우저에서 확인**

Run: 이미지 업로드 → 타이틀 입력 → 다운로드 버튼 클릭
Expected: `reels-cover-타이틀-2026-03-29.png` 파일 다운로드 (1080x1920px)

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add PNG download functionality"
```

---

### Task 8: 통합 테스트 + 마무리

**Files:**
- Modify: `index.html` (필요시 버그 수정)

- [ ] **Step 1: 전체 워크플로우 테스트**

브라우저에서 전체 플로우를 수동 테스트한다:

1. `index.html`을 브라우저에서 연다
2. 이미지를 드래그&드롭으로 업로드한다
3. 자동 색상 추천이 표시되는지 확인
4. 타이틀에 "27년 구축 투베이\n뒷베란다 현실" 입력
5. 7개 프리셋을 하나씩 선택하며 미리보기 확인
6. 릴스(9:16) 미리보기와 피드(1:1) 미리보기 비교 — 텍스트가 두 곳 모두에서 보이는지 확인
7. 로컬 폰트 업로드 후 텍스트 변경 확인
8. PNG 다운로드하여 파일 크기, 해상도 확인

Expected: 모든 단계가 정상 동작. 다운로드된 PNG가 1080x1920px.

- [ ] **Step 2: 발견된 버그 수정**

테스트 중 발견된 이슈를 수정한다.

- [ ] **Step 3: Final commit**

```bash
git add index.html
git commit -m "feat: complete reels cover generator v1"
```

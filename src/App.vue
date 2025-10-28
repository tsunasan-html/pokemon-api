<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<style>
/* ========== Reset ========== */
* { margin: 0; padding: 0; box-sizing: border-box; }

/* ========== Page Background ========== */
body {
  --bg: #0b0c10;
  background: radial-gradient(1200px 600px at 10% 0%, #0e1322 0%, var(--bg) 60%);
  margin: 0;
  padding: 0px;
}

@media (min-width: 768px) {
  body { padding: 2rem; }
}

/* ========== Loading Spinner ========== */
.spinner-wrap {
  display: grid;
  place-items: center;
  min-height: 60vh;
  animation: fadeIn 0.4s ease;
}

.spinner {
  width: 60px;
  height: 60px;
  border: 4px solid rgba(110, 168, 255, 0.15);
  border-top-color: #6ea8ff;
  border-radius: 50%;
  animation: spin 1s linear infinite, glow 1.5s ease-in-out infinite alternate;
  filter: drop-shadow(0 0 10px rgba(110,168,255,0.4));
}

@keyframes spin {
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
}

@keyframes glow {
  from { box-shadow: 0 0 5px rgba(110,168,255,0.3); }
  to { box-shadow: 0 0 20px rgba(110,168,255,0.7); }
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}

/* ========== Theme Shell ========== */
.home {
  --panel: #12141a;
  --ring: rgba(110,168,255,.35);
  --text: #e6e8ee;
  --muted: #a9b1c3;
  --gold: #eabe5c;

  min-height: 100vh;
  color: var(--text);
  padding: 32px 16px 72px;
  font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, Noto Sans, 'Helvetica Neue', Arial;
}

.container {
  width: min(1200px, 100%);
  margin-inline: auto;
  padding-inline: 12px;
}

/* ========== Header / Title ========== */
.header {
  display: grid;
  grid-template-columns: 1fr;
  gap: 8px;
  align-items: center;
  margin-bottom: 32px;
}
.title-wrap {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
}

.title {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  line-height: 1.1;
  font-weight: 800;
  letter-spacing: 1px;
  font-size: 30px;
}

.title-text {
  background: linear-gradient(90deg, #7aa2ff, #eabe5c);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  text-shadow: 0 0 12px rgba(110,168,255,.25);
}

.poke-icon {
  width: 1.2em;
  height: 1.2em;
  flex: 0 0 auto;
  vertical-align: middle;
  transform: translateY(0.06em);
  opacity: 0.9;
  filter: drop-shadow(0 0 6px rgba(110,168,255,0.15));
}

.subtitle {
  font-size: 13px;
  color: var(--muted);
  letter-spacing: .5px;
  margin-top: 6px;
}
/* ===== Header Responsive ===== */
@media (max-width: 640px) {
  .header {
    margin-bottom: 24px;
    gap: 6px;
  }

  .title-wrap {
    text-align: center;
  }

  .title {
    font-size: 1.4rem;
    gap: 4px;
    letter-spacing: 0.8px;
  }

  .poke-icon {
    width: 1em;
    height: 1em;
    transform: translateY(0.05em);
  }

  .subtitle {
    font-size: 11.5px;
    margin-top: 4px;
    line-height: 1.3;
  }
}

/* ========== Search Row (Fixed) ========== */
.search-row {
  position: sticky;
  top: 0;
  z-index: 50;
  display: flex;
  align-items: center;
  justify-content: flex-start;
  gap: 0.75rem;
  background: rgba(15, 19, 32, 0.85);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  border-bottom: 1px solid rgba(110, 168, 255, 0.15);
  padding: 12px 16px;
  margin-bottom: 24px;
  min-height: 48px;
  transition: box-shadow 0.3s ease;
}

/* スクロールで影をつけたいとき */
body.scrolled .search-row {
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.25);
}

/* 検索バー */
.search {
  flex: 1;
  background: #0f1320;
  color: var(--text);
  border: 1px solid #2a3148;
  padding: 8px 12px;
  border-radius: 10px;
  outline: none;
  max-width: 260px;
  transition: box-shadow 0.2s ease, border-color 0.2s ease;
}
.search:focus {
  box-shadow: 0 0 0 4px var(--ring);
  border-color: var(--ring);
}

/* 結果表示（1件以上のときだけ表示） */
.meta {
  color: var(--muted);
  margin: 0;
  text-align: center;
  min-width: 100px;
  font-size: 0.9rem;
}

/* モバイルでは縦並び */
@media (max-width: 640px) {
  .search-row {
    flex-direction: column;
    align-items: stretch;
    gap: 0.75rem;
    padding: 12px 14px;
    min-height: 56px;
  }

  .search {
    width: 100%;
    max-width: none;
    font-size: 16px;
    line-height: 1.3;
    padding: 12px 14px;
    border-radius: 12px;
  }

  .search::placeholder {
    font-size: 15px;
    opacity: 0.8;
  }

  .meta {
    order: -1;
    text-align: center;
    margin-top: 4px;
    font-size: 1rem;
  }
}

/* Safariの自動テキスト拡大防止 */
html, body {
  -webkit-text-size-adjust: 100%;
}

.meta.error { color: #ff6b6b; }

/* ========== No Result (中央表示) ========== */
.no-result {
  display: grid;
  place-items: center;
  min-height: 60vh;
  color: #ff6b6b;
  font-size: 1.25rem;
  font-weight: 600;
  letter-spacing: 0.5px;
  text-align: center;
  text-shadow: 0 0 4px rgba(255,107,107,0.2);
  animation: fadeIn .4s ease;
}
/* ===== No Result (スマホ用) ===== */
@media (max-width: 640px) {
  .no-result {
    min-height: 50vh;
    font-size: 1rem;
    line-height: 1.4;
    letter-spacing: 0.3px;
    padding: 0 1rem;
    text-shadow: 0 0 4px rgba(255,107,107,0.2);
  }
}
@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px); }
  to   { opacity: 1; transform: translateY(0); }
}

/* ========== Grid ========== */
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(15rem, 1fr));
  gap: 1.5rem;
}

/* ========== Card (Poké Card style) ========== */
.card {
  position: relative;
  border-radius: 18px;
  padding: 1px;
  background:
    linear-gradient(135deg, #3d4670, #171b25) padding-box,
    linear-gradient(135deg, #7aa2ff, #ffcc66) border-box;
  box-shadow:
    0 10px 30px rgba(0,0,0,.4),
    inset 0 0 0 1px rgba(255,255,255,.06);
  transition: transform .15s ease;
  cursor: pointer;
}
.card:hover { transform: translateY(-2px); }

.card::after {
  content: "";
  position: absolute; inset: 0;
  border-radius: 18px;
  background:
    radial-gradient(120px 40px at 30% -10%, rgba(255,255,255,.25), transparent 60%),
    radial-gradient(80px 30px at 80% -10%, rgba(255,255,255,.18), transparent 60%);
  pointer-events: none;
  mix-blend-mode: screen;
}

.card-frame {
  background: var(--panel);
  border-radius: 16px;
  padding: 14px;
}
/* ===== Card Frame Responsive ===== */
@media (max-width: 640px) {
  .card-frame {
    padding: 10px;
  }
}

/* Header inside card */
.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-weight: 700;
  margin-bottom: 8px;
}
.card-header .name { letter-spacing: .3px; }
.card-header .hp { color: var(--gold); }

/* ========== Card Header Responsive (横並び維持) ========== */
@media (max-width: 640px) {
  .card-header {
    justify-content: space-between;
    align-items: center;
    margin-bottom: 6px;
  }

  .card-header .name {
    font-size: 14px;
    max-width: 70%;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }

  .card-header .hp {
    font-size: 13px;
    color: var(--gold);
    flex-shrink: 0;
  }
}

/* Image area */
@media (min-width: 767px) {
  .image-wrap {
    padding: 10px;
    margin-bottom: 12px;
  }
}
.image-wrap {
  display: grid;
  place-items: center;
  background: radial-gradient(300px 140px at 50% 20%, #1a2030, #0f1320);
  border-radius: 12px;
  margin-bottom: 6px;
  padding: 3px;
  box-shadow: inset 0 0 0 1px rgba(255,255,255,.05);
}

.image-wrap img {
  width: 150px;
 
  object-fit: contain;
  filter: drop-shadow(0 8px 10px rgba(0,0,0,.45));
}
@media (min-width: 768px) {
  .image-wrap img {
    height: 150px;
  }
}
@media (max-width: 767px) {
    .image-wrap img {
  width: 100%;
}

}

/* Types & id row */
.types {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 8px;
  flex-wrap: wrap;
  margin-bottom: 10px;
}
.type {
  font-size: 12px;
  text-transform: capitalize;
  padding: 4px 8px;
  border-radius: 999px;
  border: 1px solid rgba(255,255,255,.12);
  background: #161a24;
  color: var(--text);
  box-shadow: inset 0 0 12px rgba(255,255,255,.03);
}
.id { color: var(--muted); font-variant-numeric: tabular-nums; }

/* ========== Type row responsive ========== */
@media (max-width: 640px) {
  .types {
    gap: 6px;
    margin-bottom: 8px;
  }

  .type {
    font-size: 11px;
    padding: 3px 6px;
    border-radius: 8px;
  }

  /* タグが2段になったときの隙間を詰める */
  .id {
    font-size: 11px;
  }
}
/* Type tints (rough) */
.type[data-type="fire"]      { background:#281a1a; border-color:#6d2a2a; }
.type[data-type="water"]     { background:#1a2230; border-color:#2c4970; }
.type[data-type="grass"]     { background:#1a2a20; border-color:#2b6d49; }
.type[data-type="electric"]  { background:#2a261a; border-color:#6d5f2a; }
.type[data-type="psychic"]   { background:#271a30; border-color:#5b2a6d; }
.type[data-type="ice"]       { background:#18242e; border-color:#2a5b6d; }
.type[data-type="fighting"]  { background:#2a221c; border-color:#6d4a2a; }
.type[data-type="poison"]    { background:#241a2a; border-color:#6d2a6d; }
.type[data-type="ground"]    { background:#2a241a; border-color:#6d5a2a; }
.type[data-type="flying"]    { background:#1a202a; border-color:#2a4a6d; }
.type[data-type="rock"]      { background:#252421; border-color:#5a564a; }
.type[data-type="ghost"]     { background:#1e1a2a; border-color:#4a2a6d; }
.type[data-type="dragon"]    { background:#1a2230; border-color:#2a5b6d; }
.type[data-type="dark"]      { background:#15161a; border-color:#3a3b44; }
.type[data-type="steel"]     { background:#1b2024; border-color:#3b4754; }
.type[data-type="fairy"]     { background:#2a1f27; border-color:#6d3a5a; }

/* Stats bars */
.stats { display: grid; gap: 8px; }
.stat {
  display: grid;
  grid-template-columns: 36px 1fr;
  align-items: center;
  gap: 8px;
}
.stat label { color: var(--muted); font-size: 11px; }
.bar {
  height: 8px;
  background: #131722;
  border-radius: 999px;
  position: relative;
  box-shadow: inset 0 0 0 1px rgba(255,255,255,.06);
}
.bar span {
  display: block;
  height: 100%;
  border-radius: 999px;
  background: linear-gradient(90deg, #6ea8ff, #eabe5c);
}

/* ===== Stats bars: responsive ===== */
@media (max-width: 640px) {
  .stats { gap: 3px; }

  .stat {
    grid-template-columns: 28px 1fr;
    gap: 3px;
    min-width: 0;
  }

  .stat label {
    font-size: 10px;
    line-height: 1.2;
    white-space: nowrap;
  }

  .bar {
    height: 6px;
    border-radius: 999px;
    overflow: hidden;
  }

  .bar span {
    border-radius: 999px;
  }
}
/* Buttons (future use) */
.actions { display: grid; place-items: center; margin-top: 16px; }
.btn {
  background: #0f1320;
  color: var(--text);
  border: 1px solid #2a3148;
  padding: 8px 12px;
  border-radius: 10px;
  cursor: pointer;
}
.btn:disabled { opacity:.5; cursor: not-allowed; }
.btn:hover:not(:disabled) { box-shadow: 0 0 0 4px var(--ring); }

/* ========== Responsive tweaks ========== */
@media (max-width: 640px) {
  .grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
    gap: .8rem;
  }
}

/* ========== Load More Actions / Button ========== */
.actions {
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 48px 0 8px;
  gap: 12px;
  position: relative;
}
.actions::before {
  content: "";
  position: absolute;
  top: -16px;
  left: 50%;
  transform: translateX(-50%);
  width: min(560px, 90%);
  height: 1px;
  background: linear-gradient(90deg, transparent, rgba(122,162,255,.35), transparent);
  filter: drop-shadow(0 0 4px rgba(110,168,255,.25));
}

/* ボタン本体 */
.btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: .5rem;
  margin-top: .5rem;
  padding: .75rem 1rem;
  min-width: 180px;
  border-radius: 12px;
  border: 1px solid #2a3148;
  background: #0f1320;
  color: var(--text);
  font-weight: 700;
  letter-spacing: .2px;
  line-height: 1;
  cursor: pointer;
  transition: transform .15s ease, box-shadow .2s ease, border-color .2s ease, background .2s ease, opacity .2s ease;
  box-shadow:
    0 8px 24px rgba(0,0,0,.35),
    inset 0 0 0 1px rgba(255,255,255,.04);
}

.btn:hover:not(:disabled) {
  transform: translateY(-1px);
  border-color: #3a4f7a;
  box-shadow:
    0 12px 28px rgba(0,0,0,.45),
    0 0 0 4px var(--ring),
    inset 0 0 0 1px rgba(255,255,255,.06);
}

.btn:active:not(:disabled) {
  transform: translateY(0);
  box-shadow:
    0 6px 16px rgba(0,0,0,.4),
    0 0 0 3px var(--ring);
}

.btn:focus-visible {
  outline: none;
  box-shadow: 0 0 0 4px var(--ring);
}

/* ローディング中（disabled中）はクリック不可＆スピナー表示 */
.btn:disabled {
  opacity: .6;
  cursor: not-allowed;
  transform: none;
  box-shadow: 0 6px 16px rgba(0,0,0,.25);
}

/* :disabled中に左側で回る小型スピナーを出す */
.btn:disabled::before {
  content: "";
  width: 1em;
  height: 1em;
  border-radius: 50%;
  border: 2px solid rgba(110,168,255,.25);
  border-top-color: #6ea8ff;
  display: inline-block;
  animation: spin .8s linear infinite;
}

/* ボタン文言の高さを安定させる */
.btn span {
  display: inline-block;
  transform: translateY(1px);
}

/* モバイルでは全幅にして押しやすく */
@media (max-width: 640px) {
  .actions { margin-top: 20px; }
  .btn { 
    width: 100%;
    min-width: 0;
    font-size: 14px;
    line-height: 1.3;
    padding: 12px 14px;
    border-radius: 12px;
    gap: 0.6rem;
    -webkit-tap-highlight-color: rgba(0,0,0,0);
    touch-action: manipulation;
    margin-top: 8px;
  }
}

/* ユーザーがアニメ抑制設定の場合はアニメ軽減 */
@media (prefers-reduced-motion: reduce) {
  .btn,
  .btn:hover:not(:disabled),
  .btn:active:not(:disabled) {
    transition: none;
  }
  .btn:disabled::before {
    animation: none;
  }
}
/* ====== Modal ====== */
.fade-enter-active, .fade-leave-active {
  transition: opacity .22s ease, backdrop-filter .22s ease;
}
.fade-enter-from, .fade-leave-to {
  opacity: 0;
  backdrop-filter: blur(0px);
}
.modal-overlay { backdrop-filter: blur(2px); }

/* モーダル本体の“ふわっ” */
.pop-enter-active, .pop-leave-active {
  transition:
    transform .22s cubic-bezier(.2,.7,.25,1),
    opacity .22s ease;
  will-change: transform, opacity;
}
.pop-enter-from,
.pop-leave-to {
  opacity: 0;
  transform: translateY(8px) scale(.98);
}

.pop-enter-active.modal .modal-hero img { transition: transform .28s ease, opacity .28s ease; }
.pop-enter-from.modal .modal-hero img { transform: translateY(6px); opacity: 0; }

.kv li { transition: transform .18s ease, opacity .18s ease; }
.pop-enter-from.modal .kv li { transform: translateY(4px); opacity: 0; }
.pop-enter-to.modal   .kv li { transform: translateY(0);   opacity: 1; }
.pop-enter-to.modal .kv li:nth-child(1){ transition-delay: 20ms; }
.pop-enter-to.modal .kv li:nth-child(2){ transition-delay: 40ms; }
.pop-enter-to.modal .kv li:nth-child(3){ transition-delay: 60ms; }
.pop-enter-to.modal .kv li:nth-child(4){ transition-delay: 80ms; }
.pop-enter-to.modal .kv li:nth-child(5){ transition-delay:100ms; }
.pop-enter-to.modal .kv li:nth-child(6){ transition-delay:120ms; }

@media (prefers-reduced-motion: reduce) {
  .fade-enter-active, .fade-leave-active,
  .pop-enter-active, .pop-leave-active,
  .kv li, .modal-hero img {
    transition: none !important;
    animation: none !important;
  }
}

.modal-overlay {
  position: fixed; inset: 0;
  background: rgba(0,0,0,.6);
  display: grid; place-items: center;
  padding: 16px;
  z-index: 50;
}

.modal {
  position: relative;
  width: min(920px, 96vw);
  background: #10131b;
  color: var(--text);
  border: 1px solid #27314a;
  border-radius: 16px;
  box-shadow: 0 20px 60px rgba(0,0,0,.5);
  outline: none;
  padding: 16px;
}

.modal-close {
  position: absolute; right: 20px; top: 14px;
  background: transparent; border: 0; color: #c8cde0; font-size: 20px; cursor: pointer;
}

.modal-header { text-align: center; }
.modal-header h2 { margin: 0 0 4px; }
.modal-header .tags { display: flex; gap: 8px; align-items: center; flex-wrap: wrap; }

.modal-body {
  display: grid; grid-template-columns: 340px 1fr; gap: 16px; margin-top: 12px;
}

.modal-hero { display: grid; place-items: center; background:#0f1320; border-radius: 12px; padding: 12px; }
.modal-hero img {
  object-fit: contain;
  max-width: 100%;
  height: auto;
  filter: drop-shadow(0 10px 12px rgba(0,0,0,.5));
}

.modal-info { display: grid; gap: 12px; }

.kv { list-style: none; padding: 0; margin: 0; display: grid; grid-template-columns: repeat(2, minmax(0,1fr)); gap: 8px; }
.kv li { display: flex; justify-content: space-between; background:#131828; border:1px solid #222b42; border-radius:8px; padding:8px 10px; }

.abilities, .moves { 
  text-align: center;
  background:#0f1320; border:1px solid #222b42; border-radius:8px; padding:10px; 
}
.abilities h3, .moves h3 { margin: 0 0 8px; font-size: 14px; color:#cfd6ee; }
.moves ul, .abilities ul { list-style: none; padding: 0; margin: 0; display: grid; grid-template-columns: repeat(2, minmax(0,1fr)); gap: 6px; }

.modal-actions { display: flex; justify-content: flex-end; margin-top: 12px; }

@media (min-width: 768px) {
  .modal-hero img { height: 300px; width: 300px; }
  .modal-header { text-align: center; }

  /* 背が高い時のはみ出し保険 */
  .modal-overlay {
    display: flex;
    align-items: center;
    justify-content: center;
    min-height: 100dvh;
  }
  .modal {
    max-height: calc(100dvh - 32px);
    overflow: auto;
  }
}

/* スマホ */
@media (max-width: 767px) {
  .modal-body { grid-template-columns: 1fr; }
}
@media (min-width: 610px) and (max-width: 767px) {
  .modal-hero img {
    width: 40%;
  }
}
@media (max-width: 609px) {
  .modal-hero {
      padding: 6px;
    }
  .modal-hero img {
    width: 50%;
    height: auto;
  }
}

@media (max-width: 767px) {
  .modal-info { gap: 8px; }
  .modal-header h2 { 
    margin-bottom: 1rem;
  }
  .modal-overlay {
    display: flex;
    align-items: center;
    justify-content: center;
    min-height: 100dvh;
    height: 100svh;
    overflow: auto;
  }

  .modal {
    width: 90vw;
    padding: 12px 12px 14px 12px;
    border-radius: 12px;
    max-height: calc(100dvh - 32px);
    overflow: auto;
    margin: auto;
  }

  .kv {
    grid-template-columns: repeat(2, 1fr);
    gap: 6px;
  }

  .kv li {
    padding: 6px 8px;
    border-radius: 6px;
    font-size: 13px;
  }

  .kv b {
    font-size: 13px;
  }

  .kv span {
    font-size: 13px;
  }

  .abilities, .moves {
    padding: 8px;
  }

  .abilities h3, .moves h3 {
    font-size: 13px;
    margin-bottom: 6px;
  }

  .moves ul, .abilities ul {
    gap: 4px;
    font-size: 13px;
  }
  .modal-actions {
    margin-top: 0;
  }
  .modal-body {
    gap: 8px;
  }
  .modal-body {
    margin-top: 6px;
  }
  .modal-close {
    right: 12px;
    top: 10px;
    font-size: 26px;
    padding: 6px; 
    color: #d0d6f0;
  }

  .modal-close:active {
    transform: scale(0.9);
    opacity: 0.8;
  }
  .modal-header .tags {
    justify-content: flex-start;
    gap: 8px;
    flex-wrap: wrap;
    margin-top: 4px;
  }

  .modal-header .tags .type {
    font-size: 12.5px;
    padding: 4px 8px;
    border-radius: 8px;
  }
}
</style>
import os, zipfile, textwrap

base = "/mnt/data/quantum-physics-lab"
os.makedirs(base, exist_ok=True)
files = {
"index.html": r'''<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="description" content="An interactive beginner-friendly introduction to quantum physics.">
  <title>Quantum Physics Lab</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <header class="hero">
    <nav><a class="brand" href="#">⚛ Quantum Lab</a><a href="#experiments">Experiments</a><a href="#learn">Learn</a></nav>
    <div class="hero-content">
      <p class="eyebrow">EXPLORE THE VERY SMALL</p>
      <h1>Welcome to the<br><span>Quantum World.</span></h1>
      <p class="intro">Discover the strange and fascinating rules that govern atoms, photons, and the building blocks of our universe.</p>
      <a class="primary" href="#experiments">Start exploring <span aria-hidden="true">↘</span></a>
    </div>
    <div class="orb orb-one"></div><div class="orb orb-two"></div>
  </header>

  <main>
    <section id="learn" class="section">
      <div class="section-heading"><p class="eyebrow">THE BASICS</p><h2>Big ideas, made clearer.</h2><p>Quantum physics describes how matter and energy behave at very small scales.</p></div>
      <div class="cards">
        <article class="card"><div class="symbol">〰</div><h3>Wave–particle duality</h3><p>Quantum objects can show both wave-like and particle-like behavior, depending on how they are measured.</p></article>
        <article class="card"><div class="symbol">◉</div><h3>Superposition</h3><p>A quantum system can be described as a combination of possible states until a measurement gives an outcome.</p></article>
        <article class="card"><div class="symbol">⌁</div><h3>Uncertainty</h3><p>The uncertainty principle limits how precisely certain pairs of properties, such as position and momentum, can both be known.</p></article>
      </div>
    </section>

    <section id="experiments" class="section experiment-section">
      <div class="section-heading"><p class="eyebrow">INTERACTIVE EXPERIMENT</p><h2>The double-slit experiment</h2><p>Change the setup and see how the idealized detection pattern changes.</p></div>
      <div class="experiment">
        <div class="controls">
          <h3>Experiment controls</h3>
          <label for="mode">Detection setup</label>
          <select id="mode">
            <option value="wave">No which-slit information (interference)</option>
            <option value="particle">Which-slit information available</option>
          </select>
          <label for="wavelength">Wavelength <output id="waveValue">medium</output></label>
          <input id="wavelength" type="range" min="1" max="3" value="2" step="1">
          <button id="reset" class="secondary">Reset experiment</button>
          <p class="note">Illustration only: this is a simplified model, not a laboratory prediction or a full quantum simulation.</p>
        </div>
        <div class="plot-wrap">
          <canvas id="pattern" width="760" height="330" aria-label="Illustration of a double slit detection pattern"></canvas>
          <div class="plot-caption"><span>Detection screen</span><span id="modeCaption">Interference pattern</span></div>
        </div>
      </div>
    </section>

    <section class="section facts-section">
      <div class="section-heading"><p class="eyebrow">QUICK FACTS</p><h2>Quantum, in a nutshell.</h2></div>
      <div class="fact-grid">
        <div><strong>1900</strong><p>Max Planck introduced the idea that energy exchange can occur in discrete amounts.</p></div>
        <div><strong>1905</strong><p>Albert Einstein explained the photoelectric effect using light quanta.</p></div>
        <div><strong>1920s</strong><p>Modern quantum mechanics took shape through the work of many physicists.</p></div>
      </div>
    </section>

    <section class="section glossary">
      <div class="section-heading"><p class="eyebrow">GLOSSARY</p><h2>Words worth knowing.</h2></div>
      <details><summary>Quantum</summary><p>A discrete amount of a physical quantity, such as energy in a particular process.</p></details>
      <details><summary>Photon</summary><p>A quantum of electromagnetic radiation, including visible light.</p></details>
      <details><summary>Wave function</summary><p>A mathematical description used to calculate probabilities for possible measurement outcomes.</p></details>
      <details><summary>Entanglement</summary><p>A quantum relationship between systems whose joint state cannot be described as independent states of each part.</p></details>
    </section>
  </main>
  <footer><span>⚛ Quantum Physics Lab</span><span>Made for curious minds · Educational project</span></footer>
  <script src="script.js"></script>
</body>
</html>''',
"style.css": r'''@import url('https://fonts.googleapis.com/css2?family=DM+Mono:wght@400;500&family=Manrope:wght@400;500;600;700;800&display=swap');
:root{--bg:#080d18;--panel:#111a2a;--text:#f2f5ff;--muted:#a6b2ca;--cyan:#69f0e1;--purple:#a99bff;--line:#29344a}
*{box-sizing:border-box}html{scroll-behavior:smooth}body{margin:0;background:var(--bg);color:var(--text);font-family:Manrope,system-ui,sans-serif;line-height:1.65}a{color:inherit;text-decoration:none}.hero{min-height:570px;position:relative;overflow:hidden;padding:0 7vw 90px;background:radial-gradient(ellipse at 80% 30%,#24345d 0,transparent 38%),linear-gradient(125deg,#080d18,#11142a 70%,#101c31)}nav{height:82px;display:flex;align-items:center;gap:30px;border-bottom:1px solid #ffffff16;position:relative;z-index:2}nav a{color:var(--muted);font-size:.9rem}nav .brand{margin-right:auto;color:white;font-weight:800;font-size:1.05rem}.hero-content{max-width:650px;position:relative;z-index:1;padding-top:75px}.eyebrow{font-family:"DM Mono",monospace;color:var(--cyan);font-size:.75rem;letter-spacing:.17em;font-weight:500}.hero h1{font-size:clamp(2.7rem,7vw,5.3rem);line-height:1.06;letter-spacing:-.055em;margin:15px 0 22px}.hero h1 span{color:var(--cyan)}.intro{color:var(--muted);max-width:520px;font-size:1.08rem}.primary,.secondary{display:inline-flex;align-items:center;gap:14px;border:0;border-radius:9px;padding:13px 19px;font-weight:800;cursor:pointer}.primary{background:var(--cyan);color:#07111b;margin-top:18px}.orb{position:absolute;border:1px solid #69f0e14d;border-radius:50%;pointer-events:none}.orb-one{width:330px;height:330px;right:12%;top:120px;box-shadow:0 0 80px #7167ff22,inset 0 0 60px #69f0e115}.orb-two{width:220px;height:220px;right:16%;top:175px;border-color:#a99bff8a}.section{padding:82px 7vw}.section-heading{max-width:720px;margin-bottom:30px}.section-heading h2{font-size:clamp(2rem,4vw,3rem);line-height:1.15;letter-spacing:-.04em;margin:5px 0 12px}.section-heading>p:last-child{color:var(--muted)}.cards{display:grid;grid-template-columns:repeat(3,1fr);gap:18px}.card{background:var(--panel);border:1px solid var(--line);border-radius:16px;padding:26px}.symbol{font-size:2rem;color:var(--cyan)}.card h3{margin:10px 0}.card p,.fact-grid p{color:var(--muted);margin-bottom:0}.experiment-section{background:#0c1220}.experiment{display:grid;grid-template-columns:260px 1fr;gap:22px}.controls,.plot-wrap{border:1px solid var(--line);background:var(--panel);border-radius:16px;padding:22px}.controls h3{margin-top:0}.controls label{display:block;font-size:.86rem;margin:20px 0 7px;color:var(--muted)}select,input[type=range]{width:100%}select{background:#0a1120;color:var(--text);border:1px solid var(--line);padding:11px;border-radius:8px}input[type=range]{accent-color:var(--cyan)}output{color:var(--cyan);float:right}.secondary{background:#253148;color:white;margin-top:24px}.note{font-size:.78rem;color:var(--muted);line-height:1.5}.plot-wrap{min-width:0;display:flex;flex-direction:column;justify-content:center}canvas{width:100%;height:auto;display:block}.plot-caption{display:flex;justify-content:space-between;color:var(--muted);font-size:.8rem;padding-top:10px}.fact-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:25px}.fact-grid>div{border-left:2px solid var(--purple);padding:5px 0 5px 20px}.fact-grid strong{font-size:2rem;color:var(--purple)}.glossary details{border-bottom:1px solid var(--line);padding:18px 0}.glossary summary{cursor:pointer;font-weight:700}.glossary details p{color:var(--muted);max-width:800px}footer{border-top:1px solid var(--line);padding:24px 7vw;display:flex;justify-content:space-between;gap:20px;color:var(--muted);font-size:.82rem}footer span:first-child{color:var(--text);font-weight:800}
@media(max-width:760px){.hero{padding-bottom:70px}.hero-content{padding-top:55px}.orb-one{right:-180px;opacity:.5}.orb-two{right:-130px;opacity:.5}nav{gap:15px}.cards,.fact-grid{grid-template-columns:1fr}.experiment{grid-template-columns:1fr}.section{padding-top:60px;padding-bottom:60px}footer{flex-direction:column}}
''',
"script.js": r'''const canvas = document.getElementById("pattern");
const ctx = canvas.getContext("2d");
const mode = document.getElementById("mode");
const wavelength = document.getElementById("wavelength");
const waveValue = document.getElementById("waveValue");
const modeCaption = document.getElementById("modeCaption");

function drawPattern() {
  const w = canvas.width, h = canvas.height;
  ctx.clearRect(0, 0, w, h);
  ctx.fillStyle = "#080e1b";
  ctx.fillRect(0, 0, w, h);

  // Screen glow and guide lines
  for (let x = 0; x < w; x += 40) {
    ctx.strokeStyle = "#ffffff0b";
    ctx.beginPath(); ctx.moveTo(x, 0); ctx.lineTo(x, h); ctx.stroke();
  }
  const center = h / 2;
  const scale = Number(wavelength.value);
  const interference = mode.value === "wave";
  for (let x = 0; x < w; x++) {
    const normalized = (x - w / 2) / (w / 2);
    let intensity;
    if (interference) {
      const envelope = Math.exp(-normalized * normalized * 3.5);
      const fringes = Math.cos(normalized * 27 / scale);
      intensity = envelope * (0.12 + 0.88 * fringes * fringes);
    } else {
      // Smooth single-slit-like envelope when which-slit information is available.
      intensity = Math.exp(-normalized * normalized * (5.5 / scale));
    }
    const bandHeight = 7 + intensity * (h * 0.42);
    const alpha = Math.min(1, 0.12 + intensity * 0.88);
    ctx.fillStyle = `rgba(105,240,225,${alpha})`;
    ctx.fillRect(x, center - bandHeight / 2, 1.2, bandHeight);
  }
  ctx.strokeStyle = "#a99bff";
  ctx.setLineDash([5, 7]);
  ctx.beginPath(); ctx.moveTo(w / 2, 15); ctx.lineTo(w / 2, h - 15); ctx.stroke();
  ctx.setLineDash([]);
  ctx.fillStyle = "#a6b2ca";
  ctx.font = "14px monospace";
  ctx.fillText("SCREEN", 14, 24);
  modeCaption.textContent = interference ? "Illustrative interference pattern" : "Illustrative non-interference pattern";
  waveValue.textContent = ["short", "medium", "long"][scale - 1];
}
mode.addEventListener("change", drawPattern);
wavelength.addEventListener("input", drawPattern);
document.getElementById("reset").addEventListener("click", () => {
  mode.value = "wave";
  wavelength.value = "2";
  drawPattern();
});
drawPattern();''',
"README.md": r'''# ⚛ Quantum Physics Lab

An interactive, beginner-friendly website introducing core ideas in quantum physics. It includes short concept cards, a simplified double-slit pattern illustration, and a mini glossary.

## Features

- Responsive dark-themed landing page
- Introductory explanations of wave–particle duality, superposition, and uncertainty
- Interactive double-slit illustration with selectable detection setup and wavelength
- Quick historical facts and glossary
- No build tools or dependencies required

## Run locally

1. Download or clone this repository.
2. Open `index.html` in a modern browser.

## Publish with GitHub Pages

1. Open the repository on GitHub.
2. Go to **Settings → Pages**.
3. Under **Build and deployment**, choose **Deploy from a branch**.
4. Select branch `main` and folder `/ (root)`, then click **Save**.
5. After deployment finishes, GitHub will show the published website URL in the Pages section.

## Project structure

- `index.html` — page structure and educational content
- `style.css` — visual design and responsive layout
- `script.js` — interactive canvas illustration
- `LICENSE` — MIT license
- `.gitignore` — common files to keep out of Git

## Scientific note

The double-slit visual is intentionally illustrative and qualitative. It is not a complete quantum-mechanical calculation and should not be used to predict experimental measurements. Quantum experiments depend on the physical setup and measurement procedure.

## Contributing

Suggestions, typo fixes, accessibility improvements, and scientifically careful explanations are welcome. Please keep explanations accessible and cite reliable references when adding scientific claims.
''',
"LICENSE": r'''MIT License

Copyright (c) 2026 Quantum Physics Lab contributors

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
''',
".gitignore": r'''# OS files
.DS_Store
Thumbs.db

# Editors
.vscode/
.idea/

# Logs
*.log
'''
}
for name, content in files.items():
    with open(os.path.join(base, name), "w", encoding="utf-8") as f:
        f.write(textwrap.dedent(content).lstrip())
zip_path = "/mnt/data/quantum-physics-lab.zip"
with zipfile.ZipFile(zip_path, "w", zipfile.ZIP_DEFLATED) as z:
    for name in files:
        z.write(os.path.join(base, name), arcname=f"quantum-physics-lab/{name}")
print("Created project ZIP:", zip_path)
print("Included:", ", ".join(files.keys()))

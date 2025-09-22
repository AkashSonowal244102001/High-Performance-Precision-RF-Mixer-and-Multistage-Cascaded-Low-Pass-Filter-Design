# High-Performance-Precision-RF-Mixer-and-Multistage-Cascaded-Low-Pass-Filter-Design-
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8" />
<title>70 MHz Mixer → 25–30 kHz IF Chain (RC + Op-Amp + LC)</title>
<meta name="viewport" content="width=device-width, initial-scale=1" />
<style>
  :root { --fg:#111; --muted:#555; --bg:#fff; --accent:#0b84f3; --code:#f6f8fa; }
  html,body{margin:0;padding:0;background:var(--bg);color:var(--fg);font:16px/1.5 system-ui,Segoe UI,Roboto,Helvetica,Arial,sans-serif}
  main{max-width:980px;margin:2.2rem auto;padding:0 1rem}
  h1,h2,h3{line-height:1.2;margin:1.6rem 0 .8rem}
  h1{font-size:1.9rem} h2{font-size:1.4rem} h3{font-size:1.15rem}
  p{margin:.7rem 0}
  code,kbd,pre{font-family:ui-monospace,SFMono-Regular,Consolas,Monaco,monospace}
  pre{background:var(--code);border:1px solid #e2e5e9;border-radius:8px;padding:12px;overflow:auto}
  code{background:var(--code);padding:.2em .35em;border-radius:6px}
  table{border-collapse:collapse;width:100%;margin:1rem 0}
  th,td{border:1px solid #e2e5e9;padding:.5rem .6rem;text-align:left;vertical-align:top}
  th{background:#fafbfc}
  .muted{color:var(--muted)}
  .pill{background:#eef6ff;color:#084e9b;border:1px solid #cfe2ff;border-radius:999px;padding:.1rem .5rem}
  .box{border:1px solid #e2e5e9;border-radius:10px;padding:14px;margin:1rem 0}
</style>
</head>
<body>
<main>

<h1>Switching Mixer (70 MHz) → Low-IF Chain @ 25–30 kHz</h1>

<p class="muted">
<strong>Pipeline:</strong> Single-balanced BJT mixer → <span class="pill">Stage-1</span> 2nd-order passive RC → <span class="pill">Stage-2</span> 2nd-order Sallen–Key (op-amp) → <span class="pill">Stage-3</span> 2nd-order LC “cleanup”.
</p>

<div class="box">
<pre>
RF ≈ 70.010…70.030 MHz
LO = 70.000 MHz
Desired IF = |RF - LO| ≈ 10–30 kHz (here: 25–30 kHz target)
</pre>
</div>

<h2 id="stage1">1) Stage-1 — Passive 2nd-order RC (two RCs in series)</h2>

<p><strong>Design target:</strong> make the combined −3 dB near the final fc. For two identical 1st-order sections, set each section’s corner to <code>f_section ≈ 1.56 × f_c</code>.</p>

<ul>
  <li>Per section: <code>R = 10 kΩ</code>, <code>C ≈ 360 pF</code> (330–390 pF OK)</li>
  <li>Two sections in series → ~2nd-order with gentle pass-band</li>
  <li>Buffer the next stage (op-amp input) so loading doesn’t shift poles</li>
</ul>

<h2 id="stage2">2) Stage-2 — Op-amp 2nd-order LPF (Sallen–Key, Butterworth)</h2>

<p>Use equal R’s and equal C’s; with that symmetry, Butterworth damping requires a gain of <code>K ≈ 1.586</code> because <code>Q = 1/(3 − K) = 1/√2</code>.</p>

<ul>
  <li><code>C1 = C2 = 1 nF</code></li>
  <li><code>R1 = R2 ≈ 5.62 kΩ</code> (use 5.6 kΩ)</li>
  <li><code>Gain K ≈ 1.586</code> → pick <code>R_g = 10 kΩ</code>, <code>R_f = 5.9 kΩ</code> (5.6 kΩ gives K≈1.56, still close)</li>
  <li>Op-amp GBW ≥ 1 MHz (e.g., TLV2462/LMV358/OPA350)</li>
</ul>

<h2 id="stage3">3) Stage-3 — LC 2nd-order “cleanup” (uses your 0.1 H inductors)</h2>

<p>For a simple series-L / shunt-C section targeted around 28 kHz:</p>
<pre>
f_c ≈ 1 / ( 2π · √(L · C) )
C ≈ 1 / ( (2π f_c)^2 · L )
</pre>
<p>With L = 0.1 H and f_c = 28 kHz ⇒ C ≈ 0.32 nF (≈330 pF).</p>

<p>If you want the full 6-pole ladder (Butterworth), use:</p>
<ul>
  <li><strong>C4 = C5 ≈ 240 pF</strong></li>
  <li><strong>C6 ≈ 160 pF</strong></li>
</ul>

<h2 id="mixer">4) How the BJT “switching mixer” works (plain English)</h2>
<ul>
  <li>LO drives the transistor like a switch; base sees RF.</li>
  <li>Output current = RF × sign(LO), which creates new tones at RF±LO (that’s your IF) and odd harmonics.</li>
  <li>The filters strip away everything except the desired low-IF (25–30 kHz).</li>
</ul>

<h2 id="bom">5) Example BOM (28 kHz target)</h2>
<table>
<thead><tr><th>Stage</th><th>Part</th><th>Value</th><th>Notes</th></tr></thead>
<tbody>
<tr><td>RC#1</td><td>R</td><td>10 kΩ</td><td>first RC section</td></tr>
<tr><td>RC#1</td><td>C</td><td>360 pF (330–390 pF)</td><td></td></tr>
<tr><td>RC#2</td><td>R</td><td>10 kΩ</td><td>second RC section</td></tr>
<tr><td>RC#2</td><td>C</td><td>360 pF (330–390 pF)</td><td></td></tr>
<tr><td>Sallen–Key</td><td>C1=C2</td><td>1 nF</td><td>Butterworth, K≈1.586</td></tr>
<tr><td>Sallen–Key</td><td>R1=R2</td><td>5.62 kΩ (5.6 kΩ)</td><td></td></tr>
<tr><td>Sallen–Key</td><td>Rf/Rg</td><td>5.9 kΩ / 10 kΩ</td><td>Gain set</td></tr>
<tr><td>LC cleanup</td><td>L</td><td>0.1 H</td><td>inductor</td></tr>
<tr><td>LC cleanup</td><td>C</td><td>330 pF (or C4=C5=240 pF, C6=160 pF for 6-pole)</td><td></td></tr>
</tbody>
</table>

<hr/>

<p class="muted">© YourName — MIT licensed (optional). Contributions and issues welcome.</p>

</main>
</body>
</html>

# MBSE: MATLAB/Simulink → JavaScript Converter

> Converts MATLAB simulation scripts (.m) and Simulink block diagrams (JSON) to equivalent JavaScript for integration into modern web-based simulation environments. Developed during MBSE project at EVO GmbH in collaboration with TU Augsburg — verified in employer reference (Zeugnis, Dec 2025).

---

## Project overview

Bridges the gap between classical MATLAB/Simulink engineering models and modern JavaScript simulation environments:

- **MATLAB script converter**: Parses `.m` files and generates equivalent JS (variables, loops, conditionals, functions, math)
- **Simulink block converter**: Converts Simulink block diagrams (JSON format) to a JavaScript simulation loop with Euler integration
- **Runtime helpers**: Built-in JS equivalents for MATLAB array functions (`zeros`, `ones`, `linspace`, `sum`, `mean`, `norm`)
- **Validation**: Syntax check and TODO detection on generated output
- **Example files included**: Battery ECM MATLAB script + Simulink diagram ready to convert

---

## Architecture

```
mbse-matlab-to-js/
├── src/
│   ├── matlab_parser.py     # Tokeniser, expression transformer, JS code generator
│   └── converter.py         # High-level converter CLI + Simulink block → JS
├── examples/
│   ├── matlab_input/
│   │   ├── battery_ecm.m                 # Sample MATLAB script (ready to convert)
│   │   └── battery_ecm_simulink.json     # Sample Simulink diagram (ready to convert)
│   └── js_output/           # Generated JS files appear here
├── requirements.txt
└── README.md
```

---

## Setup

```bash
git clone https://github.com/PRATdoppelEK/mbse-matlab-to-js.git
cd mbse-matlab-to-js
pip install -r requirements.txt
```

No external dependencies required beyond Python stdlib.

---

## Quickstart

### Built-in demo (no files needed — runs immediately)
```bash
python src/converter.py --demo
```

### Convert the included MATLAB example
```bash
python src/converter.py examples/matlab_input/battery_ecm.m \
  --output examples/js_output/battery_ecm.js \
  --validate
```

### Convert the included Simulink diagram
```bash
python src/converter.py examples/matlab_input/battery_ecm_simulink.json \
  --output examples/js_output/battery_ecm_sim.js \
  --validate
```

### Convert your own MATLAB file
```bash
python src/converter.py /path/to/your_script.m --output output.js --validate
```

---

## Conversion coverage

| MATLAB feature | JavaScript output |
|----------------|-------------------|
| `for k = 1:n` | `for (let k = 1; k <= n; k++)` |
| `for k = 1:step:n` | `for (let k = 1; k <= n; k += step)` |
| `if / elseif / else / end` | `if / else if / else / }` |
| `function [out] = fn(args)` | `function fn(args)` |
| `A(i)` indexing | `A[i-1]` (1→0 index conversion) |
| `.*` `./ `.^` | `*` `/` `**` |
| `sin cos exp sqrt log` | `Math.sin Math.cos ...` |
| `zeros ones linspace` | `_matlab.zeros _matlab.ones ...` |
| `disp / fprintf` | `console.log` |
| `% comment` | `// comment` |
| Simulink Integrator | Euler step `+= x * dt` |
| Simulink PID Controller | P + I + D state variables |
| Simulink Saturation | `Math.min(Math.max(...))` |

---

## Use case background

Built to support the **MBSE project at EVO GmbH / TU Augsburg**, where existing MATLAB simulation models needed to be ported to JavaScript for web-based simulation dashboards, removing MATLAB license dependencies in production environments.

---
## 📈 Results

### Demo output — MATLAB Script → JavaScript

![MATLAB to JS conversion](assets/screenshots/matlab_to_js.png)

### Demo output — Simulink Diagram → JavaScript

![Simulink to JS conversion](assets/screenshots/simulink_to_js.png)

**Conversion summary:**

| Demo | Input | Output | Validation |
|------|-------|--------|------------|
| MATLAB Script | Battery SOC Coulomb counting (.m) | `estimateSOC()` JS function + runtime helpers | ✅ valid=True |
| Simulink Diagram | BatterySOCModel (JSON) | Euler integration loop, 3600 steps | ✅ valid=True, lines=48, TODOs=0 |

**Key conversions demonstrated:**
- MATLAB `for k = 1:n` → JS `for (let k = 1; k <= n; k++)`
- MATLAB 1-based indexing → JS 0-based (`A(i)` → `A[i-1]`)
- Simulink Integrator block → Euler step (`socIntegrator += x * dt`)
- Simulink Saturation block → `Math.min(Math.max(..., 0), 1)`
- MATLAB `%` comments → JS `//` comments

---
## Tech stack

`Python 3.10+` · `Regex-based parser` · `Vanilla JavaScript output` · `No external JS dependencies`

---

## Author

**Prateek Gaur** — ML Engineer | Battery & Engineering AI | EVO GmbH (2024–2025)
[LinkedIn](https://www.linkedin.com/in/prateek-gaur-15a629b4) · [GitHub](https://github.com/PRATdoppelEK) · prateekgaur@gmx.de

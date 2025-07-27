# practice‑hackathon‑algorithm

A **hands‑on 2‑D bin‑packing challenge**: given a set of rectangular boxes described in *JSON*, the script tries to arrange them as tightly as possible inside a single container while respecting real‑world stacking constraints (support area, gravity, no “floating” boxes, etc.).

The project was written during the **Garpix summer practice hackathon** to demonstrate how an industrial optimisation task can be solved with everyday Python.

---

## Why this matters 🤔

* Warehouse logistics and e‑commerce depend on **cargo space optimisation** – fewer trucks ⇒ lower costs.  
* The official open‑source libraries usually stop at “fits / doesn’t fit”; here we go further and add **engineering rules** such as *“at least 60 % of the bottom face must be supported”*
* Everything is kept in one repo, no proprietary solvers, so you can study, hack and extend it.

---

## Quick start

```bash
# 1. Clone
git clone https://github.com/danzzzlll/garpix_practice.git
cd garpix_practice

# 2. (Optional) create a virtualenv
python -m venv .venv && source .venv/bin/activate

# 3. Install dependencies – only the Python std‑lib + a vendored copy of py3dbp
pip install -U pip

# 4. Run – **absolute path** to your JSON is mandatory
python example.py /abs/path/to/boxes.json
```

## Input file format

```
{
  "container": { "name": "Pallet‑1200‑EU", "w": 1200, "h": 2400, "d": 1500 },
  "items": [
    { "id": "A01", "w": 400, "h": 600, "d": 200, "qty": 6 },
    { "id": "B07", "w": 300, "h": 300, "d": 300, "qty": 12 }
  ]
}
```

## How it works (algorithm overview)
1. Pre‑processing
Read the JSON, unfold quantities into individual Box objects.

2. Placement loop (inside main.py):
  - Sort remaining boxes by volume.

  - For each free layer (constant Z):
 -      Try every box orientation (90° steps around X, Y, Z).

 -      Accept a placement if ≥ area_threshold % of the face area is supported by the layer below (default 0.6, tweak in constants.py).

 -      Update the free‑space map.

3. Finish when no more boxes fit; emit a list of poses {id, x, y, z, w, h, d, rot}. The loop deliberately does not back‑fill holes beneath a packed layer – it keeps the algorithm fast (O(n²) worst‑case) and avoids unstable stacks.

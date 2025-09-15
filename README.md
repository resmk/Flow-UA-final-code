# Flow-UA-final-code
A Dash application for visualizing directed graphs in 3D and simulating attacks on edge capacities. This project implements multiple attack scenarios (Budgeted and Multi-Step), highlights affected edges in red, and shows contextual paths for better understanding.



Requirements:

Python 3.9+ recommended

Install dependencies:

pip install dash dash-extensions networkx

No Node.js needed — Three.js and 3d-force-graph are loaded via CDN.


Quick Start

Prepare your graph file (see Graph File Format).

Point to it using the GRAPH_FILE environment variable, or edit the file_path in the code:

export GRAPH_FILE="/absolute/path/to/graph.txt"


Run:

python app.py


Open your browser at http://127.0.0.1:8050

If no graph file is found, the app will load a tiny fallback graph and show a warning.

Graph File Format

Each line describes a source node and its outgoing edges:

<source_id>: (target_id, capacity, attack_cost, can_attack) ...


Example:

0: (12, 10, 1.0, 1) (4, 5, 1.0, 1)
4: (1035, 20, 2.0, 1)


Node IDs are numeric (strings in the file).

can_attack = 1 means the edge is attackable.

If nodes range from 0…1035, set max_nodes=1036 in parse_adjacency_list.

Key Configuration

At the top of app.py:

SRC_LABEL = "N1"       # UI label for source
DST_LABEL = "N1036"    # UI label for destination (changeable)

RAW_SOURCE = "0"       # numeric ID of source in the file
RAW_TARGET = "1035"    # numeric ID of destination in the file


RAW_SOURCE and RAW_TARGET must match numeric IDs in your graph file.

After relabeling, these map to SRC_LABEL and DST_LABEL.

You can freely change DST_LABEL (e.g., "N1035"), but make sure all references use the constants — no hardcoded "N1036" left in the code.

Controls

Run Attack → opens attack menu:

Budgeted Attack (default budget = 150)

Multi-Step Attack (default 3 steps × 10 edges)

Show Attacked Edges → filter view to attacked edges + context paths

Jump to Source / Jump to Aim → reposition camera

RESTORE PREVIOUS → revert to last saved graph state

Legend → toggle color guide

In the 3D view:

Click node → show incoming/outgoing edges

Click edge → show capacity (orange highlight unless already attacked)

Double-click background → reset camera, clear highlights, reheat layout

Attack Logic

Budgeted Attack

Compute max flow between source/destination.

Select edges carrying positive flow.

Reduce capacities until budget is consumed (minimum 1).

Mark reduced edges red + report before/after.

Multi-Step Attack

Iteratively select top edges by flow or capacity.

Halve their capacities (minimum 1).

Apply reductions cumulatively.

Report flow/capacity impact.

Troubleshooting

“Show Attacked Edges shows nothing”

Make sure you ran an attack first.

Check DST_LABEL matches the destination node in the relabeled graph.

Confirm max_nodes=1036 if your raw IDs go up to 1035.

Search code for leftover hardcoded "N1036" — replace with DST_LABEL.

ValueError: Source/Sink not in graph

Verify RAW_SOURCE and RAW_TARGET exist in your input file.

Ensure max_nodes is not cutting them off.

Add a quick assert after relabel:

assert SRC_LABEL in G and DST_LABEL in G


requirements.txt:

dash>=2.17
dash-extensions>=1.0
networkx>=3.2



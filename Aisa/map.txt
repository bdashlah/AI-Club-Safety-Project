from pathlib import Path
import json
from collections import Counter
import matplotlib.pyplot as plt

# File path
txt_path = Path("incident_reports_sample.txt")

# Read data
incidents = []
with txt_path.open("r", encoding="utf-8") as f:
    for line in f:
        line = line.strip()
        if line:
            incidents.append(json.loads(line))

#-------------COUNTDATA-------------------------------

type_counts = Counter(i["type"] for i in incidents)
zone_counts = Counter(i["zone"] for i in incidents)
priority_counts = Counter(i["priority"] for i in incidents)

print("TOTAL REPORTS:", len(incidents))

print("\nREPORTS BY TYPE")
for k, v in type_counts.items():
    print(f"- {k}: {v}")

print("\nREPORTS BY ZONE")
for k, v in zone_counts.items():
    print(f"- {k}: {v}")

print("\nREPORTS BY PRIORITY")
for k, v in priority_counts.items():
    print(f"- {k}: {v}")

#-------------HOTSPOTPLOT----------------------------


# Extract coordinates
x = [i["x"] for i in incidents]
y = [i["y"] for i in incidents]

# Create plot
fig, ax = plt.subplots(figsize=(7, 7))

# Scatter plot
ax.scatter(x, y, s=100)

# Quadrant lines
ax.axvline(5)
ax.axhline(5)

# Limits
ax.set_xlim(0, 10)
ax.set_ylim(0, 10)

# Labels for zones
ax.text(2.5, 9.5, "On Campus", ha="center")
ax.text(7.5, 9.5, "Greek Row", ha="center")
ax.text(2.5, 0.5, "Off Campus", ha="center")
ax.text(7.5, 0.5, "Athletics Area", ha="center")

# Titles
ax.set_title("Incident Hotspots")
ax.set_xlabel("X Location")
ax.set_ylabel("Y Location")

# Label each point with ID
for incident in incidents:
    ax.annotate(
        str(incident["id"]),
        (incident["x"], incident["y"]),
        xytext=(5, 5),
        textcoords="offset points",
        fontsize=8
    )

# Save plot
plt.tight_layout()
plt.savefig("incident_hotspots.png")

print("\nSaved plot as 'incident_hotspots.png'")
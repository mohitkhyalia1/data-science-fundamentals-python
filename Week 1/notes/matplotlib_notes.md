# Matplotlib — Quick Reference Notes
**Analytics Club | Learners' Space 2026**

---

## The Anatomy of a Matplotlib Plot

```
Figure  ← the whole window / image
  └── Axes  ← the actual plot area (can have multiple per Figure)
        ├── Title
        ├── X-axis (XAxis)
        │     ├── Label
        │     └── Ticks + Tick Labels
        ├── Y-axis (YAxis)
        │     ├── Label
        │     └── Ticks + Tick Labels
        ├── Lines, Patches, Text, Images...
        └── Legend
```

Understanding this hierarchy makes everything else make sense.

---

## Basic Setup

```python
import matplotlib.pyplot as plt
import numpy as np

# Always use the OO style for anything non-trivial
fig, ax = plt.subplots(figsize=(width, height))   # figsize in inches

# Show the plot (in scripts — not needed in Jupyter)
plt.show()

# Save (do BEFORE plt.show())
fig.savefig('plot.png', dpi=150, bbox_inches='tight')
```

## Plot Types

### Line Plot
```python
ax.plot(x, y)
ax.plot(x, y,
        color='steelblue',    # color (name, hex, or RGB tuple)
        linewidth=2,           # line thickness
        linestyle='--',        # '-', '--', '-.', ':'
        marker='o',            # 'o', 's', '^', 'D', 'x', '+'
        markersize=6,
        alpha=0.8,             # transparency
        label='Series 1')
```

### Scatter Plot
```python
ax.scatter(x, y,
           c=colors,           # color array or single color
           s=sizes,            # marker size (scalar or array)
           alpha=0.7,
           edgecolors='white',
           cmap='viridis')     # colormap when c is numeric
```

### Bar Chart
```python
ax.bar(categories, values, color='steelblue', width=0.6, edgecolor='white')

# Horizontal
ax.barh(categories, values)

# Grouped bars
x = np.arange(len(categories))
width = 0.35
ax.bar(x - width/2, values1, width, label='Group 1')
ax.bar(x + width/2, values2, width, label='Group 2')
ax.set_xticks(x)
ax.set_xticklabels(categories)
```

### Histogram
```python
ax.hist(data, bins=30, color='coral', edgecolor='white', alpha=0.8,
        density=True)   # density=True normalises to probability density
```

### Pie Chart
```python
ax.pie(sizes, labels=labels, autopct='%1.1f%%', startangle=90,
       colors=['#4C72B0','#DD8452','#55A868'],
       explode=[0.05, 0, 0],   # explode first slice
       shadow=True)
ax.axis('equal')   # keep it circular
```

### Box Plot
```python
ax.boxplot(data)                  # single box
ax.boxplot([data1, data2, data3]) # multiple boxes
```

### Fill Between
```python
ax.fill_between(x, y1, y2, alpha=0.3, color='steelblue')
```

---

## Figure and Axes Setup

```python
# Single plot
fig, ax = plt.subplots(figsize=(8, 5))

# Multiple subplots
fig, axes = plt.subplots(2, 3, figsize=(12, 8))   # 2 rows, 3 cols
# Access: axes[0, 0], axes[0, 1], ..., axes[1, 2]

# Flatten for easy looping
for ax in axes.flatten():
    ax.plot(...)

# Shared axes (useful for comparison)
fig, axes = plt.subplots(1, 2, sharey=True, figsize=(10, 4))

# Fine-grained layout
fig = plt.figure(figsize=(12, 8))
ax1 = fig.add_subplot(2, 2, 1)   # 2×2 grid, position 1
ax2 = fig.add_subplot(2, 2, (2, 4))  # spans positions 2 and 4
```

---

## Labels and Annotations

```python
ax.set_title('Title', fontsize=14, fontweight='bold', pad=15)
ax.set_xlabel('X label', fontsize=12)
ax.set_ylabel('Y label', fontsize=12)
ax.legend(loc='upper right', fontsize=10, title='Legend title')

# Axis limits
ax.set_xlim(0, 100)
ax.set_ylim(-1, 1)

# Custom ticks
ax.set_xticks([0, 25, 50, 75, 100])
ax.set_xticklabels(['0%', '25%', '50%', '75%', '100%'], rotation=45)

# Annotate a specific point
ax.annotate('Peak',
            xy=(x_peak, y_peak),         # point to annotate
            xytext=(x_peak+1, y_peak+0.1),  # text position
            arrowprops=dict(arrowstyle='->'))

# Add text
ax.text(x, y, 'label', ha='center', va='bottom', fontsize=10)

# Reference lines
ax.axhline(y=0, color='black', linewidth=0.8, linestyle='--')
ax.axvline(x=0, color='black', linewidth=0.8, linestyle='--')
```

---

## Grid and Spines

```python
ax.grid(True, alpha=0.3)
ax.grid(True, axis='y', linestyle='--', alpha=0.4)  # only horizontal grid

# Remove spines (borders)
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)
```

---

## Colors

```python
# Named colors
'red', 'blue', 'green', 'steelblue', 'coral', 'navy', 'teal'

# Hex
'#4C72B0', '#DD8452'

# RGB tuple (values 0–1)
(0.3, 0.5, 0.8)

# Colormaps (for continuous data)
cmap='viridis'   # default, perceptually uniform
cmap='plasma'
cmap='coolwarm'  # diverging — good for correlation
cmap='Blues'     # sequential

# Get color from a colormap
import matplotlib.cm as cm
color = cm.viridis(0.5)   # value 0–1

# Color cycle — default colors
import matplotlib.pyplot as plt
colors = plt.rcParams['axes.prop_cycle'].by_key()['color']
```

---

## Styles

```python
# Available styles
print(plt.style.available)

# Apply a style
plt.style.use('seaborn-v0_8-whitegrid')
plt.style.use('ggplot')
plt.style.use('dark_background')

# Reset to default
plt.rcdefaults()

# Global settings
plt.rcParams['figure.figsize'] = (8, 5)
plt.rcParams['font.size'] = 12
plt.rcParams['lines.linewidth'] = 2
```

---

## Layout

```python
plt.tight_layout()              # fix overlap automatically
plt.tight_layout(pad=2.0)       # add padding
fig.subplots_adjust(hspace=0.4, wspace=0.3)  # manual spacing
fig.suptitle('Overall Title', fontsize=16, fontweight='bold')
```

---

## Saving

```python
fig.savefig('plot.png', dpi=150, bbox_inches='tight')
fig.savefig('plot.svg', bbox_inches='tight')   # vector — infinitely scalable
fig.savefig('plot.pdf', bbox_inches='tight')
```

---

## Things That Caught Me Out

| Issue | Fix |
|-------|-----|
| Plot is blank | Check that `plt.show()` is called (in scripts) |
| Labels are cut off | Add `plt.tight_layout()` before saving |
| Legend not showing | Make sure each plot call has `label=` set |
| Plot saved black | Save BEFORE calling `plt.show()` |
| Pie chart is oval | Add `ax.axis('equal')` |
| Subplots overlap | Use `fig.tight_layout()` or `fig.subplots_adjust()` |
| Ticks overlap | Add `rotation=45` to `set_xticklabels()` |

---

*Last updated: Phase 1, Learners' Space 2026*

import pandas as pd
import matplotlib.pyplot as plt
import matplotlib.font_manager as fm
import matplotlib.patches as mpatches

# Sample protein sequence
protein_sequence = "MKTLLICALAVILSTSTQGTSSNFFSPVGGVVYYYYYTDATISSQQSSNSSNDTEGILQVVCWHHHWKNGRI"

# Initialize a dictionary to hold the positions
amino_acids = 'RKHEDNQSTCYWFMLIVAGP'
data = {aa: [0] * len(protein_sequence) for aa in amino_acids}

# Fill the dictionary with occurrences
for i, aa in enumerate(protein_sequence):
    data[aa][i] = 1

# Convert the dictionary to a DataFrame
df = pd.DataFrame(data)

# Add a column for the position
df['Position'] = range(1, len(protein_sequence) + 1)

# Set the position as the index
df.set_index('Position', inplace=True)

# Set font properties to Arial
font_path = 'C:/Windows/Fonts/arial.ttf'  # Update this path to the actual location of the Arial font file

# Test loading the font
try:
    font_properties = fm.FontProperties(fname=font_path)
    print(f"Successfully loaded font from: {font_path}")
except Exception as e:
    print(f"Error loading font: {e}")
    font_properties = None  # Fallback to default if font loading fails

# Define color mappings based on charge
color_map = {
    'E': 'red', 'D': 'red',          # Acidic (negative)
    'R': 'blue', 'K': 'blue', 'H': 'blue',  # Basic (positive)
    'Q': 'green', 'S': 'green', 'Y': 'green', 'N': 'green', 'T': 'green', 'C': 'green',  # Polar uncharged
    'M': 'orange', 'L': 'orange', 'G': 'orange', 'A': 'orange', 
    'P': 'orange', 'V': 'orange', 'W': 'orange', 'I': 'orange', 'F': 'orange',  # Non-polar
}

# Plot each amino acid as a separate line with bars
fig, ax = plt.subplots(figsize=(15, 10))

# Offset for each amino acid line
offsets = {aa: i for i, aa in enumerate(amino_acids)}

# Plot each amino acid with appropriate color
for aa in amino_acids:
    positions = df.index[df[aa] == 1]
    y_values = [offsets[aa]] * len(positions)
    color = color_map.get(aa, 'black')  # Default to black if not in the color_map
    ax.bar(positions, [0.8] * len(positions), bottom=y_values, width=1.0, color=color)

# Customize the plot
ax.set_xlabel('Residue sequence number', fontproperties=font_properties)
ax.set_ylabel('Alternative amino acids', fontproperties=font_properties)
ax.set_title('Amino Acid Occurrences at Each Position in Protein Sequence', fontproperties=font_properties)
ax.set_yticks([offsets[aa] for aa in amino_acids])
ax.set_yticklabels(
    amino_acids, 
    fontproperties=font_properties, 
    fontsize=14,  # Increase font size for Y-axis labels
    va='center'   # Center-align the labels vertically on the bars
)

# Create custom legend
legend_handles = [
    mpatches.Patch(color='red', label='Acidic (E, D)'),
    mpatches.Patch(color='blue', label='Basic (R, K, H)'),
    mpatches.Patch(color='green', label='Polar uncharged (Q, S, Y, N, T, C)'),
    mpatches.Patch(color='orange', label='Non-polar (M, L, G, A, P, V, W, I, F)')
]

ax.legend(handles=legend_handles, title='Amino Acid Charge', bbox_to_anchor=(1.05, 1), loc='upper left', prop=font_properties)

# Save the plot as an SVG file with Arial font
plt.tight_layout()
plt.savefig("amino_acid_occurrences_with_legend.svg", format='svg')

# Show the plot
plt.show()

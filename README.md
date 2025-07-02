# Graph4Air: SCFL-Augmented Road Network Graphs for NO₂ Mapping

*A toolkit for building augmented road-segment graphs and training Graph Neural Networks (GCN, GAT) to predict street-level NO₂ concentrations in Amsterdam.*

---

## 📂 Repository Structure

```
├── README.md                   # This file
├── requirements.txt            # Python dependencies
├── augmented_graph_builder.ipynb   # Interactive SCFL graph builder
├── main_model.ipynb               # Interactive model training & evaluation
```

---

## 🔧 Installation

1. Clone this repository and enter the folder:

   ```bash
   git clone https://github.com/yourusername/Graph4Air.git
   cd Graph4Air
   ```
2. Create a virtual environment and install dependencies:

   ```bash
   python3 -m venv venv
   source venv/bin/activate
   pip install -r requirements.txt
   ```
---

## ⚙️ Graph Builder

The graph builder script/notebook (`augmented_graph_builder.py` or `.ipynb`) performs:

1. **Loading & reprojection** of the 50 m road‐segment GeoJSON.
2. **Feature extraction** (NO₂ target + GIS covariates).
3. **Base graph construction** (NetworkX adjacency via geometry touches).
4. **Outlier detection** (MAD‐based, 1-hop neighbors).
5. **SCFL augmentation** (`augment_grouped_far_knn`):

   * Semantic grouping of features
   * Anchor selection (`top_n`) and cosine‐similarity linking
   * Distance (`min_dist`, `max_dist`) and hop‐threshold (`hop_thresh`) filters
   * Budgeting (`per_node_cap`, `max_edges`)
6. **Hyperparameter tuning** with Optuna for both GCN and GAT variants.
7. **Saving** the two best graphs as:

   * `outputs/G_gcn_augmented.gpickle`
   * `outputs/G_gat_augmented.gpickle`

> **Adjustments**
>
> * In both script and notebook, update file paths at the top (`fp`, output directories).
> * Modify SCFL parameters or Optuna trial counts inside the tuning sections.
> * To generate a **baseline** (unaugmented) graph, set `max_edges=0` or skip augmentation.

**Run (script):**

```bash
python scripts/augmented_graph_builder.py
```

---

## 🧠 Main Model

The main model script/notebook (`main_model.py` or `.ipynb`) runs:

1. **Loading** of:

   * Processed `segment_features.csv`
   * Base and SCFL-augmented graphs (`.gpickle`)
2. **Data assembly** into `torch_geometric.data.Data` objects via `build_data_mask_missing`.
3. **Model definitions** for:

   * GCN
   * GAT
   * (Optional) non-graph baselines (e.g. Land-Use Regression)
4. **Training & evaluation**:

   * Configure hyperparameters (learning rate, epochs, dropout) in the script’s config section.
   * Generate metrics (RMSE, MAE, R²) on internal CV and external Palmes-tube test set.
   * Produce density plots and spatial NO₂ maps.

> **Adjustments**
>
> * Update graph filenames (`G_gcn_augmented.gpickle`, etc.) to match your outputs.
> * Choose which models to run by commenting/uncommenting the instantiation blocks.
> * Tweak training hyperparameters (optimizers, number of epochs, CV folds) in the “Configuration” section.

**Run (script):**

```bash
python scripts/main_model.py
```

---

## 🔧 Configuration & Customization

* **File paths** at the top of each script/notebook must be set to your local or Drive directories.
* **SCFL hyperparameters** (`top_n`, `sim_thresh`, `min_dist`, etc.) live in the Optuna objective functions.
* **Model hyperparameters** (hidden units, learning rate, epochs) can be adjusted in the `fixed_init` and optimizer config blocks.
* **Models to run** (GCN, GAT, baselines) are instantiated in clearly marked code sections—you can add or remove entries.

---

## 📊 Outputs

* Augmented graphs: `outputs/G_gcn_augmented.gpickle`, `outputs/G_gat_augmented.gpickle`
* Trained model checkpoints & logs under `outputs/`
* Evaluation metrics, density plots, and spatial maps saved in `outputs/results/`

---

## 📝 License & Contact

* **Author:** David Labee
* **Contact:** [davidlabee02@gmail.com](mailto:davidlabee02@gmail.com)
